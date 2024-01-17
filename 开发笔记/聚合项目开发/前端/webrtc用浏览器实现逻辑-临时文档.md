## 标题

```java
<!DOCTYPE html>
<html lang="en">
	<head>
		<meta charset="UTF-8" />
		<script src="https://cdn.staticfile.org/jquery/1.10.2/jquery.min.js"></script>
		<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0" />
		<title></title>
		<style>
			body {
				padding: 0;
				margin: 0;
				background-color: black;
			}
			
			video {
				transform: scaleX(-1);
				display: none;
			}
			
			#local {
				position: absolute;
				top: 15px;
				right: 10px;
				z-index: 2;
				width: 25%;
				border-radius: 10px;
			}
			#remote {
				position: fixed;
				top: 0;
				left: 0;
				right: 0;
				width: 100%;
				z-index: -1;
			}
			
			#message{
			    text-align: left;
			    color: #ffffff;
			    position: absolute;
			    top: 30%;
                left: 0; 
                width: 100%;
                line-height: 40px;
                font-size: 20px;
                padding-left: 20%;
			}
			#toolBtns{
			    position: fixed;
			    padding: 30px 0;
			    display: flex;
			    left: 0;
			    width: 100%;
			    bottom: 0;
			    right: 0;
			    text-align: center;
			    visibility: hidden;
			}
			#overBtn{
			    height: 70px;
			    width: 70px;
			    text-align: center;
			    border: none;
			    background-color: orangered;
			    color: #ffffff;
			    line-height: 70px;
			    font-size: 14px;
			    border-radius: 50%;
			    box-shadow: 0px 0px 10px;
			}
		</style>
	</head>

	<body>
		<video id="remote" autoplay></video>
		<video id="local"></video>
		<div id="message">connecting ...</div>
		<div id="toolBtns">
		    <div style="width:200px;">
		        
		    </div>
		    <div style="width:200px;">
		        <button id="overBtn" onclick="out()">OVER</button>
		    </div>
		    <div style="width:200px;">
		        
		    </div>
		</div>
	</body>
	<script type="text/javascript">
		//判断麦克风权限是否开启
        function judgeIosPermissionRecord() {
        	var result = false;
        	var avaudiosession = plus.ios.import("AVAudioSession");
        	var avaudio = avaudiosession.sharedInstance();
        	var permissionStatus = avaudio.recordPermission();
        	console.log("permissionStatus:" + permissionStatus);
        	if (permissionStatus == 1684369017 || permissionStatus == 1970168948) {
        		console.log("麦克风权限没有开启");
        	} else {
        		result = true;
        		console.log("麦克风权限已经开启");
        	}
        	plus.ios.deleteObject(avaudiosession);
        	return result;
        }
        
        // 判断相机权限是否开启
        function judgeIosPermissionCamera() {
        	var result = false;
        	var AVCaptureDevice = plus.ios.import("AVCaptureDevice");
        	var authStatus = AVCaptureDevice.authorizationStatusForMediaType('vide');
        	console.log("authStatus:" + authStatus);
        	if (authStatus == 3) {
        		result = true;
        		console.log("相机权限已经开启");
        	} else {
        		console.log("相机权限没有开启");
        	}
        	plus.ios.deleteObject(AVCaptureDevice);
        	return result;
        }
	    function requestAndroidPermission(permissionID) {
        	return new Promise((resolve, reject) => {
        		plus.android.requestPermissions(
        			[permissionID], 
        			function(resultObj) {
        				var result = 0;
        				for (var i = 0; i < resultObj.granted.length; i++) {
        					var grantedPermission = resultObj.granted[i];
        					console.log('已获得的权限' + grantedPermission);
        					result = 1
        				}
        				for (var i = 0; i < resultObj.deniedPresent.length; i++) {
        					var deniedPresentPermission = resultObj.deniedPresent[i];
        					console.log('拒接本次申请的权限' + deniedPresentPermission);
        					result = 0
        				}
        				for (var i = 0; i < resultObj.deniedAlways.length; i++) {
        					var deniedAlwaysPermission = resultObj.deniedAlways[i];
        					console.log('永久拒绝申请的权限' + deniedAlwaysPermission);
        					result = -1
        				}
        				resolve(result);
        			},
        			function(error) {
        				console.log('申请权限错误' + error.code + " = " + error.message);
        				resolve({
        					code: error.code,
        					message: error.message
        				});
        			}
        		);
        	});
        }
	    function getQueryVariable(variable) {
			var query = window.location.search.substring(1);
			var vars = query.split("&");
			for(var i = 0; i < vars.length; i++) {
				var pair = vars[i].split("=");
				if(pair[0] == variable) {
					return pair[1];
				}
			}
			return(false);
		}
	    

	    var remote=document.querySelector("#remote");
	    var local=document.querySelector("#local");
	    var message=document.querySelector("#message");
	    
        var localStream=null;
        var remoteStream=null; 
	    var trans_id=null;
	    var constraints_video={
                width: { min: 960, ideal: 1280, max: 1920 },
                height: { min: 720, ideal: 720, max: 1080 }
            };
        var is_audio = getQueryVariable('audio') || false;
	    var constraints={
	        audio: true,
    		video: constraints_video
	    };
	    var connected=false;  
	    if(is_audio){
	        constraints.video=false;
	    }
	    
	    
	    var time=0;
	    function set_message_time(){
	        time++;
	        setInterval(()=>{
	            var i=parseInt(time/60);
	            var s=time%60;
	            var showtime='';
	            if(i){
	                showtime+=i+"分";
	            }
	            showtime+=s+"秒";
	            message.innerHTML='与对方语音中: '+showtime;
	            time++;
	        },1000);
	    }
		
		function setLocalStream(stream) {
		    localStream=stream;
			if("srcObject" in local) {
				local.srcObject = stream;
			} else {
				local.src = window.URL.createObjectURL(stream);
			}
			local.onloadedmetadata = function(e) {
			    if(!is_audio && constraints.video){
			        local.style.display = "block";
			    }else{
			        local.style.display = "none";
			    }
				
				local.play();
				local.volume = 0;
			};
		}

	    if(navigator.mediaDevices === undefined) {
			navigator.mediaDevices = {};
		}
		if(navigator.mediaDevices.getUserMedia === undefined) {
			navigator.mediaDevices.getUserMedia = function(constraints) {
				var getUserMedia = navigator.webkitGetUserMedia || navigator.mozGetUserMedia;
				if(!getUserMedia) {
					return Promise.reject(new Error('getUserMedia is not implemented in this browser'));
				}
				return new Promise(function(resolve, reject) {
					getUserMedia.call(navigator, constraints, resolve, reject);
				});
			}
		}

		
		
		var socket = null;

		var iceServer={
          'iceServers':[
            {
                'url': "stun:stun.xten.com:3478",
            },
            
            {
              'url':"stun:stun.voipbuster.com:3478"
            },
            {
              'url':"stun:stun.sipgate.net:3478"
            },
            {
              'url':"stun:stun.ekiga.net:3478"
            },
            {
              'url':"stun:stun.ideasip.com:3478"
            },
            {
              'url':"stun:stun.schlund.de:3478"
            },
            {
              'url':"stun:stun.voiparound.com:3478"
            },
            {
              'url':"stun:stun.voipbuster.com:3478"
            },
            {
              'url':"stun:stun.voipstunt.com:3478"
            },
            {
              'url':"stun:stun.counterpath.com:3478"
            },
            {
              'url':"stun:stun.1und1.de:3478"
            },
            {
              'url':"stun:stun.gmx.net:3478"
            },
            {
              'url':"stun:stun.callwithus.com:3478"
            },
            {
              'url':"stun:stun.counterpath.net:3478"
            },
            {
              'url':"stun:stun.internetcalls.com:3478"
            },
            {
                "url": "turn:numb.viagenie.ca",
                "username": "webrtc@live.com",
                "credential": "muazkh"
            },
            {
                'url': 'turn:192.158.29.39:3478?transport=udp',
                'credential': 'JZEOEt2V3Qb0y27GRntt2u2PAYA=',
                'username': '28224511:1379330808'
            },
            {
                'url': 'turn:192.158.29.39:3478?transport=tcp',
                'credential': 'JZEOEt2V3Qb0y27GRntt2u2PAYA=',
                'username': '28224511:1379330808'
            },

            {
                "url": 'turn:turn.bistri.com:80',
                "credential": 'homeo',
                "username": 'homeo'
            },
            {
                "url": 'turn:turn.anyfirewall.com:443?transport=tcp',
                "credential": 'webrtc',
                "username": 'webrtc'
            },
            {
                "url": 'turn:relay.backups.cz',
                "credential": 'webrtc',
                "username": 'webrtc'
            },
            {
                "url": 'turn:relay.backups.cz?transport=tcp',
                "credential": 'webrtc',
                "username": 'webrtc'
            }
            
          ]
        };
		 
		
		var ak = getQueryVariable('ak');
		var uid= getQueryVariable('uid');
		var fid= getQueryVariable('fid') || false;
		
		var isCaller = fid!=false;
		
		
		var pc = new webkitRTCPeerConnection(iceServer);
		
		pc.onicecandidate = function(event) {
			if(event.candidate !== null) {
				socket.send(JSON.stringify({
				    "data":event.candidate,
				    "to":fid,
				    "type":"ice",
				    "from":uid,
				    "ak":ak
				}));
			}
		};
		
		
        var onEvent={
            init:function(){
                
                this.send(JSON.stringify({
				    "data":ak+uid,
				    "type":"bindUid",
				}));

    			navigator.mediaDevices.getUserMedia(constraints).then((stream)=> {
                    
    				setLocalStream(stream);
                    pc.addStream(stream);
    				if(isCaller) {
    				    setTimeout(()=>{
    				        pc.createOffer().then((desc)=>{
    				        	pc.setLocalDescription(desc);
    				        	socket.send(JSON.stringify({
    				        	    "data":desc,
    				        	    "to":fid,
    				        	    "type":"offer",
    				        	    "from":uid,
    				        	    "ak":ak
    				        	}));
    				        });
    				    },1000);
						
    				}
    			});
				
                
            },
            offer:function(json){
                
                if(json.ak==ak){
                    if(json.from && json.from != uid){
                        fid=json.from;
                        var offer = json.data;
				        pc.setRemoteDescription(new RTCSessionDescription(offer));
    					pc.createAnswer().then((desc) => {
    						pc.setLocalDescription(desc);
    					    socket.send(JSON.stringify({
							    "data":desc,
            				    "to":fid,
            				    "type":"answer",
            				    "from":uid,
            				    "ak":ak
							}));
    					});
                    }
                }
            },
            answer:function(json){
                if(json.ak==ak){
                    if(json.from && json.from != uid){
                        fid=json.from;
                        var answer = json.data;
                        connected = true;
				        pc.setRemoteDescription(new RTCSessionDescription(answer));
                    }
                }
            },
            ice:function(json){
                if(json.ak==ak){
                    if(json.from && json.from != uid){
                        fid=json.from;
                        var candidate = json.data;
				        pc.addIceCandidate(new RTCIceCandidate(candidate));
                    }
                }
            },
            close:function(json){
                if(json.ak==ak){
                    if(json.from && json.from != uid){
                        if(isCaller && trans_id){
                            //ajax for over log
        				    $.post('{:url('over')}',{
        				        id:trans_id
        				    },function (json){
        				        alert("对方已关闭");
        				        plus.webview.currentWebview().close();
        				    });
        				}else{
        				    alert("对方已关闭");
        				    plus.webview.currentWebview().close();
        				}
                    }
                }
            }
        };
        
        function init_socket(){
	        socket = new WebSocket('wss://ok.hello-shop.cn/wss'); 
		    socket.onmessage=function(e){  
				try{
					var json = JSON.parse(e.data);
					var type=json.type;
					if(type=='ping'){ 
						return;
					}
					if(onEvent[type] && typeof onEvent[type]=='function'){
						var callback=onEvent[type];
						callback.call(socket,json);
					}
				}catch(err){}
			};
	    }
	    
	    out=function (){
		    if(confirm("确定要结束吗？")){
		        socket.send(JSON.stringify({
				    "to":fid, 
				    "type":"close",
				    "from":uid,
				    "ak":ak
		        }));
		        if(isCaller && trans_id){
				    $.post('{:url('over')}',{
				        id:trans_id
				    },function (json){
				        plus.webview.currentWebview().close();
				    });
				}else{
				    setTimeout(()=>{
			            plus.webview.currentWebview().close();
			        },500);
				}
		        
		    }
	    };
		document.addEventListener("plusready", () => {
		    
		    if(plus.os.name != "iOS"){
		        requestAndroidPermission('android.permission.CAMERA').then(function(){
		            requestAndroidPermission('android.permission.RECORD_AUDIO').then(function(){
		                init_socket();
		            });
		        }); 
		    }else{
		        judgeIosPermissionRecord() && judgeIosPermissionCamera() && init_socket();
		    }
		    
            
			plus.key.addEventListener("backbutton", out); 
			
			pc.onaddstream = function(event) {
				var stream=event.stream;
				if("srcObject" in remote) {
    				remote.srcObject = stream;
    			} else {
    				remote.src = window.URL.createObjectURL(stream);
    			}
    			remoteStream=stream;
    			remote.onloadedmetadata = function(e) {
    				message.style.display="none";
    				if(!is_audio && constraints.video){
    			        remote.style.display="block";
    			    }else{
    			        remote.style.display = "none";
    			        message.style.display="block";
    			        set_message_time();
    			    }
    			    
    			    document.querySelector("#toolBtns").style.visibility="visible";
    				remote.play();
    				
				    if(isCaller){
				        //this is log!
    				    $.post('{:url('start')}',{
    				        ak:ak,
    				        offer:uid,
    				        answer:fid,
    				    },function (json){
    				        trans_id=json.data;
    				    });
    				}else{
    				    //this is log!
    				    setTimeout(()=>{
    				        $.post('{:url('start2')}',{
        				        ak:ak,
        				        offer:fid,
        				        answer:uid,
        				    });
    				    },2500);
    				}
    			};
			};

		});

	</script>

</html>
    
```
