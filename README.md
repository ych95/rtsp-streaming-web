# Node.js와 FFmpeg를 활용한 실시간 RTSP 스트리밍 웹페이지

<img src="https://i.ibb.co/ttnjX4d/rtsp-streaming-web.png" alt="rtsp-streaming-web" border="0">

이 프로젝트는 **Node.js**와 **FFmpeg**를 활용하여<br>실시간 RTSP 스트리밍을 처리하고<br>웹페이지로 제공하는 시스템을 구현한 것입니다.  
이 웹 애플리케이션은 RTSP 스트리밍을 HTTP로 변환하고,<br> WebSocket을 통해 실시간으로 웹 브라우저에 스트리밍을 제공합니다.

## ㅁ RTSP 주소 출처
[공공데이터포털-충청남도 천안시_교통정보 CCTV](https://www.data.go.kr/data/15063717/fileData.do?recommendDataYn=Y)

## ㅁ 폴더 구조
**rtsp-streaming-web**<br>
│<br>
├── **rtsp**<br>
│   └── public<br>
│        ├── node_modules            # 공통 Node.js 모듈<br>
│        ├── rtsp_test_1<br>
│        │   └── index.html          # 스트림 HTML 파일<br>
│        ├── package.json            # 프로젝트 의존성 파일<br>
│        ├── package-lock.json<br>
│        └── Server.js               # 서버 코드<br>
│<br>
└── **rtsp_test_1**<br>
      ├── node_modules            # 스트림의 Node.js 모듈<br>
      ├── index.js                # 스트림 관련 서버 코드<br>
      ├── package.json            # 스트림 의존성 파일<br>
      └── package-lock.json<br>
	 
## ㅁ 주요 기능
- **RTSP 스트리밍**: RTSP URL을 통해 실시간 스트리밍을 수신하고 처리합니다.
- **웹 인터페이스**: 웹 브라우저에서 실시간으로 비디오 스트리밍을 시청할 수 있습니다.
- **FFmpeg** 활용: FFmpeg를 이용하여 RTSP 스트림을 HTTP로 변환하여 웹에서 재생합니다.
- **WebSocket**: Node.js의 WebSocket 서버를 사용하여 실시간 스트리밍을 클라이언트에게 전달합니다.

## ㅁ 사용 기술
- **Node.js**: 서버 사이드 개발 및 WebSocket 서버 구현
- **FFmpeg**: RTSP 스트림을 HTTP 형식으로 변환하여 전송
- **WebSocket**: 실시간 데이터 전송
- **HTML5 Video**: 웹에서 실시간 스트리밍을 재생하기 위해 `<canvas>`를 활용

## ㅁ 설치 방법
이 프로젝트를 로컬에서 실행하려면 아래 단계를 따라주세요:

### 1. Node.js 설치
Node.js를 설치하려면 [Node.js 공식 웹사이트](https://nodejs.org/ko/download)에서 다운로드 후 설치합니다.

### 2. FFmpeg 설치
FFmpeg는 RTSP 스트리밍을 처리하는 데 사용됩니다. [FFmpeg 공식 웹사이트](https://ffmpeg.org/download.html)에서 다운로드 후 설치합니다.
FFmpeg를 설치한 후, 시스템 환경 변수에 FFmpeg의 경로를 추가해야 합니다.

### 3. GitHub 저장소 클론
```bash
git clone https://github.com/ych95/rtsp-streaming-web.git
cd rtsp-streaming-web
```

## ㅁ 코드 설명

### 1. 서버 코드 (`server.js`)
```javascript
const express = require('express');
const path = require('path');
const app = express();

// 정적 파일 제공 경로 설정
app.use('/test1', express.static(path.join(__dirname, 'public', 'rtsp_test_1')));
app.use('/test2', express.static(path.join(__dirname, 'public', 'rtsp_test_2')));
app.use('/test3', express.static(path.join(__dirname, 'public', 'rtsp_test_3')));

// 라우트 설정
app.get('/test1', (req, res) => {
    res.sendFile(path.join(__dirname, 'rtsp_test_1', 'index.html'));
});

app.get('/test2', (req, res) => {
    res.sendFile(path.join(__dirname, 'rtsp_test_2', 'index.html'));
});

app.get('/test3', (req, res) => {
    res.sendFile(path.join(__dirname, 'rtsp_test_3', 'index.html'));
});


// 서버 시작
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
    console.log(`Server is running on port ${PORT}`);
});
```

### 2. 클라이언트 코드 (`public\rtsp_test_1\index.html`)
```HTML
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>익산 CCTV</title>
	<script src="https://cdnjs.cloudflare.com/ajax/libs/jsmpeg/0.2/jsmpg.js"></script>
	<style>
        /* 부모 요소에 flexbox를 적용하여 가로로 배열 */
		#streams {
			display: flex;
			flex-wrap: wrap; /* 화면 너비 따라 줄 바꿈 */
		}
		.stream-container {
			margin-bottom: 20px; /* 스트림 간의 간격을 조정 */
		}
		canvas {
			width: 200px;
			height: 160px;
			border: 2px solid black;
			margin-left:5px;
		}
		.title {
            text-align: center;
			font-size: 18px;
			font-weight: bold;
			margin-bottom: 5px; /* 제목과 캔버스 간의 간격 조정 */
			margin: 5px;
		}
	</style>
</head>
<body>
	<div id="streams"></div>
	<script>
		const streamUrls = [
			{ name: '세집매 삼거리', port: 10000 },
			{ name: '서부역 입구 삼거리', port: 10001 },
			{ name: '역말 오거리', port: 10002 },
			{ name: '천안로사거리', port: 10003 },
			{ name: '상방죽안오거리', port: 10004 },
			{ name: '천안역', port: 10005 },
			{ name: '남부오거리', port: 10006 },
			{ name: '교보사거리', port: 10007 },
			{ name: '청삼교차로', port: 10008 },
			{ name: '신방삼거리', port: 10009 },
			{ name: '이마트 사거리', port: 10010 },
			{ name: '쌍용사거리', port: 10011 },
			{ name: '봉서사거리', port: 10012 },
			{ name: '구상골 사거리', port: 10013 },
			{ name: '인쇄창사거리', port: 10014 },
			{ name: '부성초교사거리', port: 10015 }
		];

		const streamsContainer = document.getElementById('streams');

		streamUrls.forEach(streamInfo => {
			const container = document.createElement('div');
			container.className = 'stream-container';

			const title = document.createElement('div');
			title.className = 'title';
			title.innerText = streamInfo.name;

			const canvas = document.createElement('canvas');
			canvas.id = `camera${streamInfo.port}`;

			container.appendChild(title);
			container.appendChild(canvas);
			streamsContainer.appendChild(container);

			const client = new WebSocket(`ws://192.168.100.200:${streamInfo.port}`);
			new jsmpeg(client, {
				canvas: canvas,
				autoplay: true,
				audio: false,
				loop: true
			});
		});
	</script>
</body>
</html>
```

### 3. RTSP 스트림 처리 코드 (`rtsp_test_1\index.js`)
```javascript
const Stream = require('node-rtsp-stream');

// RTSP 스트림 URL 목록
const Stream = require('node-rtsp-stream');

// RTSP 스트림 URL 목록
var rtspList = [
        { url: 'rtsp://210.99.70.120:1935/live/cctv001.stream', name: '세집매 삼거리' },
        { url: 'rtsp://210.99.70.120:1935/live/cctv002.stream', name: '서부역 입구 삼거리' },
        { url: 'rtsp://210.99.70.120:1935/live/cctv003.stream', name: '역말 오거리' },
        { url: 'rtsp://210.99.70.120:1935/live/cctv004.stream', name: '천안로사거리' },
        { url: 'rtsp://210.99.70.120:1935/live/cctv006.stream', name: '방죽안오거리' },
        { url: 'rtsp://210.99.70.120:1935/live/cctv007.stream', name: '천안역' },
        { url: 'rtsp://210.99.70.120:1935/live/cctv008.stream', name: '남부오거리' },
        { url: 'rtsp://210.99.70.120:1935/live/cctv009.stream', name: '교보사거리' },
        { url: 'rtsp://210.99.70.120:1935/live/cctv010.stream', name: '청삼교차로' },
        { url: 'rtsp://210.99.70.120:1935/live/cctv011.stream', name: '신방삼거리' },
        { url: 'rtsp://210.99.70.120:1935/live/cctv012.stream', name: '이마트 사거리' },
        { url: 'rtsp://210.99.70.120:1935/live/cctv013.stream', name: '쌍용사거리' },
        { url: 'rtsp://210.99.70.120:1935/live/cctv014.stream', name: '봉서사거리' },
        { url: 'rtsp://210.99.70.120:1935/live/cctv015.stream', name: '구상골 사거리' },
        { url: 'rtsp://210.99.70.120:1935/live/cctv016.stream', name: '인쇄창사거리' },
        { url: 'rtsp://210.99.70.120:1935/live/cctv017.stream', name: '부성초교사거리' }
];

var rtspListLength = rtspList.length;
for(var i=0; i<rtspListLength; i++){
        openStream(rtspList[i], i);
}

function openStream(obj, index){
var stream = new Stream({
        name: 'name',
        streamUrl : obj.url,
        wsPort: 10000 + index,
        ffmpegOptions: {
                '-stats': '',
                '-r': 30,
        }
});

obj.stream = stream;

}
```

## ㅁ 네트워크 통신 경로

<img src="https://i.ibb.co/YTVqcNkG/RTSP.png" alt="RTSP" border="0" width="100%">

> 1. 웹서버 실행 (server.js)<br>
Express 서버가 시작되어 포트 3000에서 대기합니다.<br>
사용자가 /test1 경로로 접근할 때 index.html을 제공하도록 설정합니다.
```
    const PORT = process.env.PORT || 3000;
    app.listen(PORT, () => {
        console.log(`Server is running on port ${PORT}`);
    });
```

> 2. RTSP 스트림 변환 및 WebSocket 서버 시작 (index.js)<br>
RTSP 스트림 목록을 순회하여 각 스트림에 대해 웹 소켓 포트를 지정하고 FFmpeg 옵션을 적용하여 새로운 스트림 객체를 생성한 후,<br>
해당 스트림 객체를 원래 스트림 정보 객체에 저장합니다.<br>
WebSocket 서버가 포트 10000부터 시작하여 각 스트림에 대해 하나씩 생성됩니다.
```
streamUrls.forEach((streamInfo, index) => {
    new Stream({
        name: streamInfo.name,
        streamUrl: streamInfo.url,
        wsPort: 10000 + index, // 포트를 10000부터 시작하여 순차적으로 증가시킵니다.
        ffmpegOptions: {
            '-stats': '',
            '-r': 72
        }
    });
});
```

> 3. 클라이언트가 웹 페이지 요청 (index.html)<br>
사용자가 브라우저에서 http://<서버 IP>:3000/test1로 접근합니다.<br>
Express 서버는 index.html 파일을 클라이언트에 전송합니다.
```
app.get('/test1', (req, res) => {
    res.sendFile(path.join(__dirname, 'rtsp_test_1', 'index.html'));
});
```

> 4. 웹 페이지 로드 및 WebSocket 클라이언트 생성 (index.html)<br>
클라이언트의 브라우저는 index.html 파일을 로드하고, JavaScript 코드를 실행합니다.<br>
각 스트림에 대해 WebSocket 클라이언트를 생성하여 ws://192.168.100.200:3000 등의 주소로 연결합니다.
```
const client = new WebSocket(`ws://${window.location.hostname}:${10000 + streamInfo.index}`);
new jsmpeg(client, {
    canvas: canvas,
    autoplay: true,
    audio: false,
    loop: true
});
```

> 5. WebSocket을 통한 스트림 데이터 수신 및 렌더링 (index.html)<br>
WebSocket 클라이언트는 서버와 연결되어 스트림 데이터를 수신합니다.<br>
jsmpeg 라이브러리를 통해 스트림 데이터를 canvas에 렌더링합니다.
```
new jsmpeg(client, {
    canvas: canvas,
    autoplay: true,
    audio: false,
    loop: true
});
```

