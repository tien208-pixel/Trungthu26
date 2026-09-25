<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Trung Thu Vui Vẻ</title>
    <link href="https://fonts.googleapis.com/css2?family=Dancing+Script:wght@700&family=Quicksand:wght@500;700&display=swap" rel="stylesheet">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            background-color: #0b0f19; /* Màu nền trời đêm */
            overflow: hidden;
            font-family: 'Quicksand', sans-serif;
            color: white;
            height: 100vh;
            width: 100vw;
        }

        /* --- CẢNH 1: MÀN HÌNH CHỜ --- */
        #intro-scene {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 10;
            background: radial-gradient(circle at center, #1a2a42 0%, #0b0f19 100%);
            transition: opacity 1.5s ease-in-out, transform 1.5s ease-in-out;
        }

        #intro-scene.hidden {
            opacity: 0;
            transform: scale(1.5);
            pointer-events: none;
        }

        .moon-container {
            cursor: pointer;
            position: relative;
            animation: pulse 2s infinite alternate;
        }

        .moon {
            width: 150px;
            height: 150px;
            background: #ffe39f;
            border-radius: 50%;
            box-shadow: 0 0 50px #ffdf88, 0 0 100px #ffdf88, inset -20px -20px 40px rgba(0,0,0,0.2);
        }

        .instruction {
            margin-top: 30px;
            font-size: 1.2rem;
            color: #ffb6c1;
            text-shadow: 0 0 10px #ffb6c1;
            animation: bounce 2s infinite;
        }

        /* --- CẢNH 2: KHÔNG GIAN TRÔI NỔI --- */
        #floating-scene {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            opacity: 0;
            pointer-events: none;
            transition: opacity 2s ease-in-out;
            transition-delay: 0.5s; /* Đợi màn 1 mờ đi một chút */
            z-index: 5;
        }

        #floating-scene.active {
            opacity: 1;
            pointer-events: auto;
        }

        /* Style chung cho các phần tử trôi nổi */
        .floating-item {
            position: absolute;
            white-space: nowrap;
            will-change: transform;
            animation-name: floatAround;
            animation-iteration-count: infinite;
            animation-direction: alternate;
            animation-timing-function: ease-in-out;
        }

        /* Phân loại màu sắc và hiệu ứng cho chữ */
        .text-type-1 { 
            font-family: 'Dancing Script', cursive; 
            color: #ff7eb3; 
            text-shadow: 0 0 10px #ff7eb3, 0 0 20px #ff7eb3; 
        }
        .text-type-2 { 
            font-family: 'Dancing Script', cursive; 
            color: #7efaff; 
            text-shadow: 0 0 10px #7efaff, 0 0 20px #7efaff; 
        }
        .text-type-3 { 
            font-family: 'Dancing Script', cursive; 
            color: #ffdf88; 
            text-shadow: 0 0 10px #ffdf88, 0 0 20px #ffdf88; 
        }

        /* Keyframes */
        @keyframes pulse {
            0% { transform: scale(1); box-shadow: 0 0 30px #ffdf88; }
            100% { transform: scale(1.05); box-shadow: 0 0 70px #ffdf88, 0 0 120px #ffdf88; }
        }

        @keyframes bounce {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-10px); }
        }

        @keyframes floatAround {
            0% { transform: translate(0, 0) rotate(-5deg) scale(1); }
            33% { transform: translate(30px, -40px) rotate(5deg) scale(1.1); }
            66% { transform: translate(-20px, 20px) rotate(-3deg) scale(0.9); }
            100% { transform: translate(10px, -10px) rotate(2deg) scale(1.05); }
        }
        
        /* Hiệu ứng lấp lánh cho nền */
        .stars {
            position: absolute;
            width: 100%;
            height: 100%;
            background-image: 
                radial-gradient(2px 2px at 20px 30px, #eee, rgba(0,0,0,0)),
                radial-gradient(2px 2px at 40px 70px, #fff, rgba(0,0,0,0)),
                radial-gradient(2px 2px at 50px 160px, #ddd, rgba(0,0,0,0)),
                radial-gradient(2px 2px at 90px 40px, #fff, rgba(0,0,0,0)),
                radial-gradient(2px 2px at 130px 80px, #fff, rgba(0,0,0,0)),
                radial-gradient(2px 2px at 160px 120px, #ddd, rgba(0,0,0,0));
            background-repeat: repeat;
            background-size: 200px 200px;
            z-index: 1;
            opacity: 0.3;
        }
    </style>
</head>
<body>

    <div id="intro-scene">
        <div class="stars"></div>
        <div class="moon-container" id="trigger-moon">
            <div class="moon"></div>
        </div>
        <div class="instruction">Chạm vào mặt trăng nhé!</div>
    </div>

    <div id="floating-scene">
        <div class="stars"></div>
        </div>

    <script>
        const introScene = document.getElementById('intro-scene');
        const floatingScene = document.getElementById('floating-scene');
        const moon = document.getElementById('trigger-moon');

        // Dữ liệu chữ và icon theo yêu cầu
        const contents = [
            { text: "Trung thu vui vẻ nha mỹ nữ", type: "text-type-1" },
            { text: "Mãi cute nhó", type: "text-type-2" },
            { text: "Thành công trên con đường phía trước", type: "text-type-3" },
            { text: "❤️", type: "icon" },
            { text: "🐰", type: "icon" },
            { text: "⭐", type: "icon" }
        ];

        // Sự kiện click vào mặt trăng
        moon.addEventListener('click', () => {
            // Ẩn cảnh 1
            introScene.classList.add('hidden');
            // Hiện cảnh 2
            floatingScene.classList.add('active');
            
            // Bắt đầu tạo các phần tử trôi nổi
            generateFloatingElements();
        });

        function generateFloatingElements() {
            const numElements = 45; // Số lượng phần tử muốn hiển thị trên màn hình
            
            for (let i = 0; i < numElements; i++) {
                // Chọn ngẫu nhiên 1 nội dung từ mảng contents
                const randomContent = contents[Math.floor(Math.random() * contents.length)];
                
                const el = document.createElement('div');
                el.classList.add('floating-item');
                el.innerText = randomContent.text;
                
                if(randomContent.type !== "icon") {
                    el.classList.add(randomContent.type);
                }

                // --- Random kích thước, độ mờ, vị trí và thời gian animation ---
                
                // Vị trí ngẫu nhiên trên màn hình (0% đến 90% để không bị tràn ra ngoài)
                const posX = Math.random() * 90;
                const posY = Math.random() * 90;
                el.style.left = `${posX}%`;
                el.style.top = `${posY}%`;

                // Độ lớn ngẫu nhiên (chữ xa thì nhỏ, gần thì to)
                let scale = Math.random() * 1.5 + 0.8; // từ 0.8 đến 2.3
                if(randomContent.type === "icon") {
                    scale = Math.random() * 1.5 + 1; // Icon to hơn một chút
                }
                el.style.fontSize = `${scale}rem`;

                // Độ mờ ngẫu nhiên (tạo chiều sâu)
                const opacity = Math.random() * 0.7 + 0.3; // từ 0.3 đến 1
                el.style.opacity = opacity;

                // Thời gian trôi ngẫu nhiên để không bị đồng đều (từ 5s đến 15s)
                const animDuration = Math.random() * 10 + 5; 
                el.style.animationDuration = `${animDuration}s`;
                
                // Độ trễ ngẫu nhiên (tránh việc giật cục lúc bắt đầu)
                const animDelay = Math.random() * -5;
                el.style.animationDelay = `${animDelay}s`;

                // Hướng chuyển động ngẫu nhiên
                if(Math.random() > 0.5) {
                    el.style.animationDirection = 'alternate-reverse';
                }

                floatingScene.appendChild(el);
            }
        }
    </script>

</body>
</html>
