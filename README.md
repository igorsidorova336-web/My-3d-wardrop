   <!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>3D Гардероб + Монеты</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { overflow: hidden; font-family: Arial; background: #1a1a2e; user-select: none; -webkit-tap-highlight-color: transparent; }
        #coinCounter {
            position: absolute;
            top: 15px;
            left: 15px;
            background: rgba(0,0,0,0.6);
            backdrop-filter: blur(10px);
            padding: 10px 18px 10px 14px;
            border-radius: 30px;
            border: 1px solid rgba(255,215,0,0.3);
            color: #ffd700;
            font-size: 20px;
            font-weight: bold;
         display: flex;
            align-items: center;
            gap: 8px;
            z-index: 15;
            box-shadow: 0 4px 15px rgba(0,0,0,0.4);
            pointer-events: none;
        }
        #coinCounter span {
            font-size: 22px;
        }
        #status {
            position: absolute; top: 15px; left: 50%; transform: translateX(-50%);
            color: white; background: rgba(0,0,0,0.6); padding: 8px 20px;
            border-radius: 20px; font-size: 13px; backdrop-filter: blur(5px);
            border: 1px solid rgba(255,255,255,0.1); pointer-events: none; z-index: 10;
            white-space: nowrap;
        }
        #clickEffect {
            position: absolute;
            color: #ffd700;
            font-size: 28px;
            font-weight: bold;
            pointer-events: none;
            z-index: 20;
            opacity: 0;
            transition: none;
            text-shadow: 0 0 20px rgba(255,215,0,0.6);
        }
        #menuToggle {
            position: absolute; bottom: 130px; right: 20px;
            background: rgba(52,152,219,0.8); backdrop-filter: blur(10px);
            border: 1px solid rgba(255,255,255,0.2); color: white;
            width: 60px; height: 60px; border-radius: 50%; font-size: 28px;
            cursor: pointer; z-index: 20; touch-action: manipulation;
            box-shadow: 0 4px 20px rgba(0,0,0,0.4); transition: all 0.3s;
        }
        #menuToggle:active { transform: scale(0.9); }
        #wardrobeMenu {
            position: absolute; bottom: -100%; left: 0; width: 100%;
            background: rgba(20,20,40,0.95); backdrop-filter: blur(20px);
            border-top: 1px solid rgba(255,255,255,0.1);
            padding: 20px 16px 30px; z-index: 30;
            transition: bottom 0.4s cubic-bezier(0.4,0,0.2,1);
            max-height: 65vh; overflow-y: auto; border-radius: 20px 20px 0 0;
        }
        #wardrobeMenu.open { bottom: 0; }
        #wardrobeMenu::-webkit-scrollbar { width: 3px; }
        #wardrobeMenu::-webkit-scrollbar-thumb { background: rgba(255,255,255,0.2); border-radius: 10px; }
        .menu-header {
            display: flex; justify-content: space-between; align-items: center;
            color: white; margin-bottom: 15px; padding-bottom: 10px;
            border-bottom: 1px solid rgba(255,255,255,0.05);
        }
        .menu-header h3 { font-size: 18px; font-weight: 600; }
        .menu-close {
            background: rgba(255,255,255,0.1); border: none; color: white;
            font-size: 24px; width: 40px; height: 40px; border-radius: 50%;
            cursor: pointer; touch-action: manipulation;
        }
        .menu-close:active { transform: scale(0.9); }
        .category { margin-bottom: 16px; }
        .category-title {
            color: rgba(255,255,255,0.6); font-size: 12px;
            text-transform: uppercase; letter-spacing: 1px; margin-bottom: 8px;
        }
        .items { display: flex; gap: 10px; flex-wrap: wrap; }
        .item-btn {
            background: rgba(255,255,255,0.08); border: 2px solid transparent;
            color: white; padding: 8px 16px; border-radius: 20px; font-size: 13px;
            cursor: pointer; transition: all 0.2s; touch-action: manipulation;
            user-select: none; white-space: nowrap;
        }
        .item-btn:active { transform: scale(0.92); }
        .item-btn.active { border-color: #3498db; background: rgba(52,152,219,0.25); }
        .color-picker { display: flex; gap: 8px; flex-wrap: wrap; }
        .color-dot {
            width: 36px; height: 36px; border-radius: 50%;
            border: 2px solid rgba(255,255,255,0.2); cursor: pointer;
            transition: all 0.2s; touch-action: manipulation;
        }
        .color-dot:active { transform: scale(0.85); }
        .color-dot.active { border-color: #3498db; transform: scale(1.1); box-shadow: 0 0 20px rgba(52,152,219,0.3); }
        #info {
            position: absolute; bottom: 10px; left: 0; width: 100%;
            text-align: center; color: rgba(255,255,255,0.3); font-size: 11px;
            pointer-events: none; z-index: 5;
        }
        #clickHint {
            position: absolute; bottom: 80px; left: 50%; transform: translateX(-50%);
            color: rgba(255,255,255,0.2); font-size: 12px; pointer-events: none; z-index: 5;
            text-align: center;
            background: rgba(0,0,0,0.2); padding: 4px 14px; border-radius: 12px;
        }
        @media (max-width: 500px) {
            #menuToggle { bottom: 110px; right: 15px; width: 54px; height: 54px; font-size: 24px; }
            #wardrobeMenu { padding: 16px 12px 24px; max-height: 60vh; }
            .item-btn { padding: 6px 12px; font-size: 12px; }
            .color-dot { width: 30px; height: 30px; }
            #coinCounter { font-size: 16px; padding: 6px 12px 6px 10px; top: 10px; left: 10px; }
            #coinCounter span { font-size: 18px; }
        }
    </style>
</head>
<body>
    <div id="coinCounter">
        <span>🪙</span> <span id="coinCount">0</span>
    </div>
    <div id="status">👤 Персонаж</div>
    <div id="clickEffect">+1 🪙</div>
    <button id="menuToggle">👕</button>
    <div id="wardrobeMenu">
        <div class="menu-header">
            <h3>👗 Гардероб</h3>
            <button class="menu-close" id="closeMenu">✕</button>
        </div>
        <div class="category">
            <div class="category-title">🎨 Цвет кожи</div>
            <div class="color-picker" id="skinColors">
                <div class="color-dot active" style="background:#f5cba7;" data-color="#f5cba7"></div>
                <div class="color-dot" style="background:#ffcc99;" data-color="#ffcc99"></div>
                <div class="color-dot" style="background:#8D6E63;" data-color="#8D6E63"></div>
                <div class="color-dot" style="background:#D7A86E;" data-color="#D7A86E"></div>
                <div class="color-dot" style="background:#e8b4b4;" data-color="#e8b4b4"></div>
            </div>
        </div>
        <div class="category">
            <div class="category-title">👕 Футболки</div>
            <div class="items" id="shirts">
                <button class="item-btn active" data-shirt="#3498db">🔵 Синяя</button>
                <button class="item-btn" data-shirt="#e74c3c">🔴 Красная</button>
                <button class="item-btn" data-shirt="#2ecc71">🟢 Зелёная</button>
                <button class="item-btn" data-shirt="#f1c40f">🟡 Жёлтая</button>
                <button class="item-btn" data-shirt="#9b59b6">🟣 Фиолетовая</button>
                <button class="item-btn" data-shirt="#e67e22">🟠 Оранжевая</button>
            </div>
        </div>
        <div class="category">
            <div class="category-title">👖 Штаны</div>
            <div class="items" id="pants">
                <button class="item-btn active" data-pants="#2c3e50">⚫ Тёмные</button>
                <button class="item-btn" data-pants="#34495e">🔵 Синие</button>
                <button class="item-btn" data-pants="#7f8c8d">⚪ Серые</button>
                <button class="item-btn" data-pants="#8B0000">🔴 Бордовые</button>
                <button class="item-btn" data-pants="#228B22">🟢 Хаки</button>
            </div>
        </div>
        <div class="category">
            <div class="category-title">👟 Обувь</div>
            <div class="items" id="shoes">
                <button class="item-btn active" data-shoes="#1a1a2e">⚫ Чёрные</button>
                <button class="item-btn" data-shoes="#8B4513">🟤 Коричневые</button>
                <button class="item-btn" data-shoes="#ffffff">⚪ Белые</button>
                <button class="item-btn" data-shoes="#c0392b">🔴 Красные</button>
            </div>
        </div>
        <div class="category">
            <div class="category-title">🧢 Аксессуары</div>
            <div class="items" id="accessories">
                <button class="item-btn" data-accessory="hat">🧢 Шляпа</button>
                <button class="item-btn" data-accessory="glasses">👓 Очки</button>
                <button class="item-btn" data-accessory="bowtie">🎀 Бабочка</button>
            </div>
        </div>
    </div>
    <div id="clickHint">👆 Нажми на персонажа — получи монету!</div>
    <div id="info">Потяни пальцем → вращать</div>
    <script type="importmap">
        {
            "imports": {
                "three": "https://cdn.jsdelivr.net/npm/three@0.160.0/build/three.module.js",
                "three/addons/": "https://cdn.jsdelivr.net/npm/three@0.160.0/examples/jsm/"
            }
        }
    </script>
    <script type="module">
        import * as THREE from 'three';
        import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
        // ---- СЦЕНА ----
        const scene = new THREE.Scene();
        scene.background = new THREE.Color(0x1a1a2e);
        const camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000);
        camera.position.set(2, 1.5, 4);
        const renderer = new THREE.WebGLRenderer({ antialias: true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
        renderer.shadowMap.enabled = true;
        renderer.toneMapping = THREE.ACESFilmicToneMapping;
        renderer.toneMappingExposure = 1.2;
        document.body.appendChild(renderer.domElement);
        // ---- УПРАВЛЕНИЕ ----
        const controls = new OrbitControls(camera, renderer.domElement);
        controls.target.set(0, 0.8, 0);
        controls.enableDamping = true;
        controls.dampingFactor = 0.08;
        controls.minDistance = 1.5;
        controls.maxDistance = 8;
        controls.update();
        // ---- СВЕТ ----
        const ambient = new THREE.AmbientLight(0x404060, 0.5);
        scene.add(ambient);
        const main = new THREE.DirectionalLight(0xffeedd, 1.5);
        main.position.set(3, 5, 4);
        main.castShadow = true;
        scene.add(main);
        const fill = new THREE.DirectionalLight(0x4488ff, 0.4);
        fill.position.set(-2, 1, -3);
        scene.add(fill);
        const back = new THREE.DirectionalLight(0xff8844, 0.3);
        back.position.set(0, 0.5, -4);
        scene.add(back);
        // ---- ПОЛ ----
        const plane = new THREE.Mesh(
            new THREE.CircleGeometry(2.5, 20),
            new THREE.MeshStandardMaterial({ color: 0x2a2a4a, transparent: true, opacity: 0.4, roughness: 0.7, side: THREE.DoubleSide })
        );
        plane.rotation.x = -Math.PI / 2;
        plane.position.y = -0.1;
        plane.receiveShadow = true;
        scene.add(plane);
        // ---- ПЕРСОНАЖ ----
        const character = new THREE.Group();
        const body = new THREE.Mesh(
            new THREE.BoxGeometry(0.5, 0.7, 0.3),
            new THREE.MeshStandardMaterial({ color: 0x3498db, roughness: 0.4, metalness: 0.1 })
        );
        body.position.y = 0.35;
        body.castShadow = true;
        character.add(body);
        const head = new THREE.Mesh(
            new THREE.SphereGeometry(0.25, 16, 16),
            new THREE.MeshStandardMaterial({ color: 0xf5cba7, roughness: 0.3 })
        );
        head.position.y = 0.9;
        head.castShadow = true;
        character.add(head);
        // Глаза
        const eyeMat = new THREE.MeshStandardMaterial({ color: 0xffffff });
        const pupilMat = new THREE.MeshStandardMaterial({ color: 0x1a1a2e });
        const eyeL = new THREE.Mesh(new THREE.SphereGeometry(0.06, 8, 8), eyeMat);
        eyeL.position.set(-0.1, 0.95, 0.22);
        character.add(eyeL);
        const pupilL = new THREE.Mesh(new THREE.SphereGeometry(0.035, 8, 8), pupilMat);
        pupilL.position.set(-0.1, 0.95, 0.27);
        character.add(pupilL);
        const eyeR = new THREE.Mesh(new THREE.SphereGeometry(0.06, 8, 8), eyeMat);
        eyeR.position.set(0.1, 0.95, 0.22);
        character.add(eyeR);
        const pupilR = new THREE.Mesh(new THREE.SphereGeometry(0.035, 8, 8), pupilMat);
        pupilR.position.set(0.1, 0.95, 0.27);
        character.add(pupilR);
        // Руки
        const armMat = new THREE.MeshStandardMaterial({ color: 0xf5cba7, roughness: 0.4 });
        const armL = new THREE.Mesh(new THREE.CylinderGeometry(0.05, 0.07, 0.45, 8), armMat);
        armL.position.set(-0.32, 0.6, 0);
        armL.rotation.z = 0.3;
        armL.rotation.x = -0.3;
        armL.castShadow = true;
        character.add(armL);
        const armR = new THREE.Mesh(new THREE.CylinderGeometry(0.05, 0.07, 0.45, 8), armMat);
        armR.position.set(0.32, 0.6, 0);
        armR.rotation.z = -0.3;
        armR.rotation.x = 0.3;
        armR.castShadow = true;
        character.add(armR);
        // Ноги
        const legMat = new THREE.MeshStandardMaterial({ color: 0x2c3e50, roughness: 0.6 });
        const legL = new THREE.Mesh(new THREE.CylinderGeometry(0.07, 0.09, 0.35, 8), legMat);
        legL.position.set(-0.13, 0.0, 0);
        legL.castShadow = true;
        character.add(legL);
        const legR = new THREE.Mesh(new THREE.CylinderGeometry(0.07, 0.09, 0.35, 8), legMat);
        legR.position.set(0.13, 0.0, 0);
        legR.castShadow = true;
        character.add(legR);
      // Обувь
        const shoeMat = new THREE.MeshStandardMaterial({ color: 0x1a1a2e, roughness: 0.8 });
        const shoeL = new THREE.Mesh(new THREE.BoxGeometry(0.12, 0.05, 0.18), shoeMat);
        shoeL.position.set(-0.13, -0.2, 0.04);
        shoeL.castShadow = true;
        character.add(shoeL);
        const shoeR = new THREE.Mesh(new THREE.BoxGeometry(0.12, 0.05, 0.18), shoeMat);
        shoeR.position.set(0.13, -0.2, 0.04);
        shoeR.castShadow = true;
        character.add(shoeR);
        // Шляпа
        const hatGroup = new THREE.Group();
        const hatBase = new THREE.Mesh(new THREE.CylinderGeometry(0.32, 0.35, 0.06, 12), new THREE.MeshStandardMaterial({ color: 0x8B0000 }));
        hatBase.position.y = 0.05;
        hatGroup.add(hatBase);
        const hatTop = new THREE.Mesh(new THREE.CylinderGeometry(0.2, 0.22, 0.2, 12), new THREE.MeshStandardMaterial({ color: 0x8B0000 }));
        hatTop.position.y = 0.2;
        hatGroup.add(hatTop);
        hatGroup.position.y = 1.05;
        hatGroup.visible = false;
        character.add(hatGroup);
       // Очки
        const glassesGroup = new THREE.Group();
        const glassMat = new THREE.MeshStandardMaterial({ color: 0x1a1a2e, metalness: 0.8 });
        const glassL2 = new THREE.Mesh(new THREE.TorusGeometry(0.08, 0.025, 8, 12), glassMat);
        glassL2.position.set(-0.1, 0.92, 0.22);
        glassL2.rotation.x = 0.2;
        glassesGroup.add(glassL2);
        const glassR2 = new THREE.Mesh(new THREE.TorusGeometry(0.08, 0.025, 8, 12), glassMat);
        glassR2.position.set(0.1, 0.92, 0.22);
        glassR2.rotation.x = 0.2;
        glassesGroup.add(glassR2);
        glassesGroup.visible = false;
        character.add(glassesGroup);
        // Бабочка
        const bowtieGroup = new THREE.Group();
        const bowMat = new THREE.MeshStandardMaterial({ color: 0xe74c3c });
        const bowL2 = new THREE.Mesh(new THREE.BoxGeometry(0.08, 0.04, 0.02), bowMat);
        bowL2.position.set(-0.05, 0.6, 0.16);
        bowtieGroup.add(bowL2);
        const bowR2 = new THREE.Mesh(new THREE.BoxGeometry(0.08, 0.04, 0.02), bowMat);
        bowR2.position.set(0.05, 0.6, 0.16);
        bowtieGroup.add(bowR2);
        bowtieGroup.visible = false;
        character.add(bowtieGroup);
        character.position.y = 0;
        scene.add(character);
        // ---- СЧЁТЧИК МОНЕТ ----
        let coins = 0;
        const coinDisplay = document.getElementById('coinCount');
        const clickEffect = document.getElementById('clickEffect');
       function addCoin(event) {
            coins++;
            coinDisplay.textContent = coins;
            // Анимация +1
            const rect = renderer.domElement.getBoundingClientRect();
            const x = event.clientX || event.touches?.[0]?.clientX || window.innerWidth/2;
            const y = event.clientY || event.touches?.[0]?.clientY || window.innerHeight/2;
            clickEffect.style.left = (x - 30) + 'px';
            clickEffect.style.top = (y - 30) + 'px';
            clickEffect.style.opacity = 1;
            clickEffect.style.transform = 'scale(1)';
            clickEffect.textContent = '+1 🪙';
            // Анимация вверх и исчезновение
            let startY = y - 30;
            let opacity = 1;
            const interval = setInterval(() => {
                startY -= 3;
                opacity -= 0.025;
                clickEffect.style.top = startY + 'px';
                clickEffect.style.opacity = opacity;
                if (opacity <= 0) {
                    clearInterval(interval);
                    clickEffect.style.opacity = 0;
                }
            }, 20);
        }
        // ---- ОБРАБОТЧИКИ НАЖАТИЙ ----
        // Нажатие на персонажа (через Raycaster)
        const raycaster = new THREE.Raycaster();
        const pointer = new THREE.Vector2();
        function onPointerDown(event) {
            const clientX = event.clientX || event.touches?.[0]?.clientX;
            const clientY = event.clientY || event.touches?.[0]?.clientY;
            if (clientX === undefined) return;
            const rect = renderer.domElement.getBoundingClientRect();
            pointer.x = ((clientX - rect.left) / rect.width) * 2 - 1;
            pointer.y = -((clientY - rect.top) / rect.height) * 2 + 1;

raycaster.setFromCamera(pointer, camera);
            const intersects = raycaster.intersectObjects(character.children, true);
            if (intersects.length > 0) {
                addCoin(event);
                // Небольшая вибрация (если поддерживается)
                if (navigator.vibrate) navigator.vibrate(20);
            }
        }
        // Наведение мыши (для ПК) — по клику
        renderer.domElement.addEventListener('click', onPointerDown);
        // Для телефона — касание
        renderer.domElement.addEventListener('touchstart', onPointerDown, { passive: true });
        // ---- КНОПКИ ГАРДЕРОБА ----
        document.querySelectorAll('#skinColors .color-dot').forEach(dot => {
            dot.onclick = () => {
                document.querySelectorAll('#skinColors .color-dot').forEach(d => d.classList.remove('active'));
                dot.classList.add('active');
                const c = new THREE.Color(dot.dataset.color);
                head.material.color.set(c);
                armL.material.color.set(c);
                armR.material.color.set(c);
                document.getElementById('status').textContent = '🎨 Цвет кожи изменён';
                setTimeout(() => document.getElementById('status').textContent = '👤 Персонаж', 1000);
            };
        });
        document.querySelectorAll('#shirts .item-btn').forEach(btn => {
            btn.onclick = () => {
                document.querySelectorAll('#shirts .item-btn').forEach(b => b.classList.remove('active'));
                btn.classList.add('active');
                body.material.color.set(new THREE.Color(btn.dataset.shirt));
                document.getElementById('status').textContent = '👕 Футболка надета';
                setTimeout(() => document.getElementById('status').textContent = '👤 Персонаж', 1000);
            };
        });
        document.querySelectorAll('#pants .item-btn').forEach(btn => {
            btn.onclick = () => {
                document.querySelectorAll('#pants .item-btn').forEach(b => b.classList.remove('active'));
                btn.classList.add('active');
                const c = new THREE.Color(btn.dataset.pants);
                legL.material.color.set(c);
                legR.material.color.set(c);
                document.getElementById('status').textContent = '👖 Штаны надеты';
                setTimeout(() => document.getElementById('status').textContent = '👤 Персонаж', 1000);
            };
        });
        document.querySelectorAll('#shoes .item-btn').forEach(btn => {
            btn.onclick = () => {
                document.querySelectorAll('#shoes .item-btn').forEach(b => b.classList.remove('active'));
                btn.classList.add('active');
                const c = new THREE.Color(btn.dataset.shoes);
                shoeL.material.color.set(c);
                shoeR.material.color.set(c);
                document.getElementById('status').textContent = '👟 Обувь надета';
                setTimeout(() => document.getElementById('status').textContent = '👤 Персонаж', 1000);
            };
        });
        document.querySelectorAll('#accessories .item-btn').forEach(btn => {
            btn.onclick = () => {
                const type = btn.dataset.accessory;
                if (type === 'hat') {
                    hatGroup.visible = !hatGroup.visible;
                    btn.style.borderColor = hatGroup.visible ? '#3498db' : 'transparent';
                    document.getElementById('status').textContent = hatGroup.visible ? '🧢 Шляпа надета' : '🧢 Шляпа снята';
                } else if (type === 'glasses') {
                    glassesGroup.visible = !glassesGroup.visible;
                    btn.style.borderColor = glassesGroup.visible ? '#3498db' : 'transparent';
                    document.getElementById('status').textContent = glassesGroup.visible ? '👓 Очки надеты' : '👓 Очки сняты';
                } else if (type === 'bowtie') {
                    bowtieGroup.visible = !bowtieGroup.visible;
                    btn.style.borderColor = bowtieGroup.visible ? '#3498db' : 'transparent';
                    document.getElementById('status').textContent = bowtieGroup.visible ? '🎀 Бабочка надета' : '🎀 Бабочка снята';
                }
                setTimeout(() => document.getElementById('status').textContent = '👤 Персонаж', 1200);
            };
        });
        // ---- МЕНЮ ----
        const menu = document.getElementById('wardrobeMenu');
        document.getElementById('menuToggle').onclick = () => {
            menu.classList.toggle('open');
            document.getElementById('menuToggle').textContent = menu.classList.contains('open') ? '✕' : '👕';
        };
        document.getElementById('closeMenu').onclick = () => {
            menu.classList.remove('open');
            document.getElementById('menuToggle').textContent = '👕';
        };
        // ---- АДАПТАЦИЯ ----
        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });
        // ---- АНИМАЦИЯ ----
        function animate() {
            requestAnimationFrame(animate);
            controls.update();
            renderer.render(scene, camera);
        }
        animate();
        setTimeout(() => {
            document.getElementById('status').textContent = '👆 Нажми на персонажа!';
            setTimeout(() => document.getElementById('status').textContent = '👤 Персонаж', 3000);
        }, 500);
    </script>
</body>
</html>

