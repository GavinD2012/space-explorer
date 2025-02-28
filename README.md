# space-explorer
travel the milky way
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Solar System Explorer Prototype</title>
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
        #landButton {
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

<div id="info">Use WASD to move, Mouse to look around | Click "Land" to land on planets</div>
<button id="landButton" onclick="landOnPlanet()">Land</button>

<script>
    let scene, camera, renderer, controls, landed = false;
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
        createPlanet(6371, 'https://upload.wikimedia.org/wikipedia/commons/8/83/Earthmap1000x500.jpg', 1500000);
        createPlanet(3389, 'https://upload.wikimedia.org/wikipedia/commons/0/02/OSIRIS_Mars_true_color.jpg', 2279000);
        createPlanet(69911, 'https://upload.wikimedia.org/wikipedia/commons/c/c1/Jupiter_New_Horizons.jpg', 7785000);

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

        function checkLanding() {
            let landedPlanet = null;
            planets.forEach((planet) => {
                let dist = camera.position.distanceTo(planet.mesh.position);
                if (dist < planet.mesh.geometry.parameters.radius * 1.5) {
                    landedPlanet = planet;
                }
            });

            return landedPlanet;
        }

        function landOnPlanet() {
            let planet = checkLanding();
            if (planet) {
                landed = true;
                camera.position.set(planet.position + 500, 0, 0);
                camera.lookAt(planet.mesh.position);

                if (planet.mesh.geometry.parameters.radius === 6371) {
                    window.location.href = "https://earth.google.com/web/";
                }
            } else {
                alert("Get closer to a planet to land!");
            }
        }

        function animate() {
            requestAnimationFrame(animate);
            updateMovement();
            renderer.render(scene, camera);
        }

        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });

        animate();
    }
</script>

</body>
</html>
