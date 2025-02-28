# space-explorer
travel the milky way
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Solar System Explorer</title>
    <style>
        body { margin: 0; overflow: hidden; font-family: Arial, sans-serif; background-color: black; }
        canvas { display: block; }
        #info {
            position: absolute;
            top: 10px;
            left: 10px;
            color: white;
            background: rgba(0, 0, 0, 0.5);
            padding: 10px;
            border-radius: 5px;
        }
        #warpButton {
            position: absolute;
            top: 50px;
            left: 10px;
            padding: 10px;
            background: white;
            border: none;
            cursor: pointer;
        }
    </style>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
</head>
<body onload="init()">

<div id="info">Use WASD to move, Mouse to look around | Click "Warp" to travel to a new star</div>
<button id="warpButton" onclick="warpToSystem()">Warp to Alpha Centauri</button>

<script>
    let scene, camera, renderer, landed = false;
    let planets = [];
    let playerSpeed = 5000;

    function init() {
        scene = new THREE.Scene();
        camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 100000000);
        renderer = new THREE.WebGLRenderer({ antialias: true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        document.body.appendChild(renderer.domElement);

        // Sunlight
        const light = new THREE.PointLight(0xffffff, 2, 100000000);
        light.position.set(0, 0, 0);
        scene.add(light);

        // Function to create planets
        function createPlanet(size, textureURL, position) {
            const texture = new THREE.TextureLoader().load(textureURL);
            const geometry = new THREE.SphereGeometry(size, 32, 32);
            const material = new THREE.MeshStandardMaterial({ map: texture });
            const planet = new THREE.Mesh(geometry, material);
            planet.position.set(position, 0, 0);
            scene.add(planet);
            planets.push({ mesh: planet, position });
            return planet;
        }

        // Create Sun & Planets
        createPlanet(695700, 'https://upload.wikimedia.org/wikipedia/commons/2/28/Solarsystemscope_texture_2k_sun.jpg', 0);
        createPlanet(2440, 'https://upload.wikimedia.org/wikipedia/commons/3/3d/Mercury_in_true_color.jpg', 579100);
        createPlanet(6052, 'https://upload.wikimedia.org/wikipedia/commons/e/e5/Venus-real_color.jpg', 1082000);
        createPlanet(6371, 'https://upload.wikimedia.org/wikipedia/commons/8/83/Earthmap1000x500.jpg', 1500000);
        createPlanet(3389, 'https://upload.wikimedia.org/wikipedia/commons/0/02/OSIRIS_Mars_true_color.jpg', 2279000);
        createPlanet(69911, 'https://upload.wikimedia.org/wikipedia/commons/c/c1/Jupiter_New_Horizons.jpg', 7785000);
        createPlanet(58232, 'https://upload.wikimedia.org/wikipedia/commons/2/29/Saturn-true-color.jpg', 14340000);
        createPlanet(25362, 'https://upload.wikimedia.org/wikipedia/commons/3/3d/Uranus2.jpg', 28710000);
        createPlanet(24622, 'https://upload.wikimedia.org/wikipedia/commons/5/56/Neptune_Full.jpg', 44950000);

        // Camera starts near Earth
        camera.position.set(1500500, 0, 200000);

        // Movement Controls
        let move = { forward: false, backward: false, left: false, right: false };
        document.addEventListener('keydown', (e) => {
            if (e.key === 'w') move.forward = true;
            if (e.key === 's') move.backward = true;
            if (e.key === 'a') move.left = true;
            if (e.key === 'd') move.right = true;
        });
        document.addEventListener('keyup', (e) => {
            if (e.key === 'w') move.forward = false;
            if (e.key === 's') move.backward = false;
            if (e.key === 'a') move.left = false;
            if (e.key === 'd') move.right = false;
        });

        // Mouse Look
        document.addEventListener('mousemove', (event) => {
            camera.rotation.y -= event.movementX * 0.002;
            camera.rotation.x -= event.movementY * 0.002;
        });

        function updateMovement() {
            if (landed) return;
            let direction = new THREE.Vector3();
            camera.getWorldDirection(direction);
            if (move.forward) camera.position.addScaledVector(direction, playerSpeed);
            if (move.backward) camera.position.addScaledVector(direction, -playerSpeed);
            if (move.left) camera.position.x -= playerSpeed;
            if (move.right) camera.position.x += playerSpeed;
        }

        function warpToSystem() {
            alert("Warping to Alpha Centauri...");
            camera.position.set(4.3 * 9460730472580, 0, 0); // Moves 4.3 light-years away
        }

        function animate() {
            requestAnimationFrame(animate);
            updateMovement();
            renderer.render(scene, camera);
        }

        animate();
    }
</script>

</body>
</html>
