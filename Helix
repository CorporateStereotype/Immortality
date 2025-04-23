<!DOCTYPE html>
<html>
<head>
    <title>X-T/X-A Synthesis & Quantum</title>
    <style>
        body { margin: 0; }
        #info { position: absolute; top: 10px; left: 10px; color: white; }
        #quantumCanvas { position: absolute; top: 10px; right: 10px; width: 300px; height: 200px; background: rgba(0, 0, 0, 0.7); }
        #quantumData { position: absolute; top: 220px; right: 10px; color: white; font-size: 12px; }
    </style>
</head>
<body>
    <div id="info">Press Space for Legend, Enter for Quantum</div>
    <canvas id="quantumCanvas"></canvas>
    <div id="quantumData">Quantum Data Loading...</div>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://unpkg.com/three@0.128.0/examples/js/controls/OrbitControls.js"></script>
    <script>
        // Scene setup
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        const renderer = new THREE.WebGLRenderer();
        renderer.setSize(window.innerWidth, window.innerHeight);
        document.body.appendChild(renderer.domElement);

        // Controls
        const controls = new THREE.OrbitControls(camera, renderer.domElement);
        controls.enableDamping = true;
        controls.dampingFactor = 0.05;

        // Lighting
        const light = new THREE.PointLight(0xffffff, 1, 100);
        light.position.set(10, 10, 10);
        scene.add(light);

        // Materials
        const carbonMat = new THREE.MeshBasicMaterial({ color: 0x808080 });
        const nitrogenMat = new THREE.MeshBasicMaterial({ color: 0x0000ff });
        const oxygenMat = new THREE.MeshBasicMaterial({ color: 0xff0000 });
        const fluorineMat = new THREE.MeshBasicMaterial({ color: 0xffff00 });
        const hydrogenMat = new THREE.MeshBasicMaterial({ color: 0xffffff });
        const bondMat = new THREE.MeshBasicMaterial({ color: 0xcccccc });
        const hBondMat = new THREE.LineDashedMaterial({ color: 0xaaaaaa, dashSize: 0.2, gapSize: 0.2 });
        const wireframeMat = new THREE.LineBasicMaterial({ color: 0x00ffff });
        const glowMat = new THREE.MeshBasicMaterial({ color: 0xffff00, transparent: true, opacity: 0.5 });
        const polyMat = new THREE.MeshBasicMaterial({ color: 0xff0000 });
        const synthMat = new THREE.MeshBasicMaterial({ color: 0x800080, transparent: true });
        const entangleMat = new THREE.LineBasicMaterial({ color: 0x00ffff });
        const correctMat = new THREE.MeshBasicMaterial({ color: 0x00ff00 });

        // Geometries
        const atomGeo = new THREE.SphereGeometry(0.2, 32, 32);
        const bondGeo = new THREE.CylinderGeometry(0.05, 0.05, 1, 32);
        const glowGeo = new THREE.SphereGeometry(0.3, 32, 32);
        const polyGeo = new THREE.SphereGeometry(0.5, 32, 32);
        const correctGeo = new THREE.SphereGeometry(0.1, 16, 16);

        // Base Template
        const xtTemplate = [
            [-2, 0, 0, carbonMat], [-1.5, 1, 0, nitrogenMat], [-0.5, 1, 0, carbonMat], [-0.5, 2, 0, oxygenMat],
            [0.5, 0, 0, nitrogenMat], [0.5, -1, 0, hydrogenMat], [0, -1, 0, carbonMat], [0, -2, 0, oxygenMat],
            [-1, -1, 0, carbonMat], [-1, -2, 0, fluorineMat], [-1.5, 0, 0, carbonMat], [-2.5, 0, 0, hydrogenMat]
        ];
        const xaTemplate = [
            [2, 0, 0, nitrogenMat], [2.5, 1, 0, carbonMat], [2.5, 2, 0, fluorineMat], [3.5, 0, 0, nitrogenMat],
            [3, -1, 0, carbonMat], [2, -1, 0, carbonMat], [1.5, 0, 0, carbonMat], [1, 1, 0, nitrogenMat],
            [0.5, 1, 0, hydrogenMat], [1.5, -2, 0, nitrogenMat], [2.5, -2, 0, carbonMat], [3, -3, 0, hydrogenMat],
            [1, -1, 0, nitrogenMat], [0.5, -2, 0, hydrogenMat]
        ];

        // Build Original Helix (10 Pairs)
        const helix = [];
        for (let i = 0; i < 10; i++) {
            const z = i * 3.4;
            const angle = (i * 36) * Math.PI / 180;
            const xtAtoms = xtTemplate.map(([x, y, zBase, mat]) => {
                const atom = new THREE.Mesh(atomGeo, mat);
                const newX = x * Math.cos(angle) - y * Math.sin(angle);
                const newY = x * Math.sin(angle) + y * Math.cos(angle);
                atom.position.set(newX, newY, z);
                atom.userData = { basePos: atom.position.clone(), isFluorine: mat === fluorineMat };
                scene.add(atom);
                return atom;
            });
            const xaAtoms = xaTemplate.map(([x, y, zBase, mat]) => {
                const atom = new THREE.Mesh(atomGeo, mat);
                const newX = x * Math.cos(angle) - y * Math.sin(angle);
                const newY = x * Math.sin(angle) + y * Math.cos(angle);
                atom.position.set(newX, newY, z);
                atom.userData = { basePos: atom.position.clone(), isFluorine: mat === fluorineMat };
                scene.add(atom);
                return atom;
            });
            helix.push({ xt: xtAtoms, xa: xaAtoms });
        }

        // Synthesis Strand
        const synthHelix = [];
        const synthPairs = [];

        // Bonds (Base 0)
        const bondPairs = [
            [0, 1], [1, 2], [2, 3], [2, 4], [4, 5], [4, 6], [6, 7], [6, 8], [8, 9], [8, 10], [10, 11], [10, 0],
            [12, 13], [13, 14], [13, 15], [15, 16], [16, 17], [17, 18], [18, 19], [17, 20], [20, 21], [21, 22], [21, 16], [20, 23], [23, 24]
        ].map(([i, j]) => [helix[0].xt.concat(helix[0].xa)[i], helix[0].xt.concat(helix[0].xa)[j]]);
        bondPairs.forEach(([a1, a2]) => {
            const bond = new THREE.Mesh(bondGeo, bondMat);
            bond.position.lerpVectors(a1.position, a2.position, 0.5);
            bond.scale.z = a1.position.distanceTo(a2.position);
            bond.lookAt(a2.position);
            scene.add(bond);
        });

        // H-Bonds
        const hBonds = helix.map(pair => {
            const bonds = [];
            const pairs = [[pair.xt[5], pair.xa[0]], [pair.xt[3], pair.xa[8]]];
            pairs.forEach(([a1, a2]) => {
                const hBond = new THREE.Line(new THREE.BufferGeometry().setFromPoints([a1.position, a2.position]), hBondMat);
                hBond.computeLineDistances();
                bonds.push(hBond);
                scene.add(hBond);
            });
            return bonds;
        }).flat();

        // Helix Wireframe
        const helixPoints1 = [], helixPoints2 = [];
        for (let t = 0; t <= 2 * Math.PI; t += 0.1) {
            const x1 = Math.cos(t), y1 = Math.sin(t), z1 = (34 * t) / (2 * Math.PI);
            const x2 = -Math.cos(t), y2 = -Math.sin(t), z2 = z1;
            helixPoints1.push(new THREE.Vector3(x1, y1, z1));
            helixPoints2.push(new THREE.Vector3(x2, y2, z2));
        }
        const helixGeo1 = new THREE.BufferGeometry().setFromPoints(helixPoints1);
        const helixGeo2 = new THREE.BufferGeometry().setFromPoints(helixPoints2);
        const helixWireframe1 = new THREE.Line(helixGeo1, wireframeMat);
        const helixWireframe2 = new THREE.Line(helixGeo2, wireframeMat);
        scene.add(helixWireframe1);
        scene.add(helixWireframe2);

        // Polymerase
        const polymerase = new THREE.Mesh(polyGeo, polyMat);
        polymerase.position.set(0, 0, 0);
        scene.add(polymerase);

        // Effects
        const particles = new THREE.Group();
        scene.add(particles);

        // Quantum Overlay
        const quantumGroup = new THREE.Group();
        for (let i = 0; i < 10; i++) {
            const qubitBox = new THREE.BoxGeometry(5, 3, 0.5);
            const qubitEdges = new THREE.EdgesGeometry(qubitBox);
            const qubit = new THREE.LineSegments(qubitEdges, wireframeMat);
            qubit.position.z = i * 3.4;
            qubit.rotation.z = (i * 36) * Math.PI / 180;
            quantumGroup.add(qubit);
        }

        // Numerical Data
        const makeTextSprite = (message) => {
            const canvas = document.createElement('canvas');
            const ctx = canvas.getContext('2d');
            ctx.font = '20px Arial';
            ctx.fillStyle = 'white';
            ctx.fillText(message, 0, 20);
            const texture = new THREE.Texture(canvas);
            texture.needsUpdate = true;
            const spriteMat = new THREE.SpriteMaterial({ map: texture });
            const sprite = new THREE.Sprite(spriteMat);
            sprite.scale.set(5, 2, 1);
            return sprite;
        };
        const bondSprite = makeTextSprite("C-F: 1.35 Å, H-Bond: 2.8 Å");
        const vibSprite = makeTextSprite("Vib Amp: 0.1 Å (F), 0.2 Å (others)");
        const synthSprite = makeTextSprite("Synthesis Active");
        scene.add(bondSprite);
        scene.add(vibSprite);
        scene.add(synthSprite);

        // Quantum Circuit Canvas
        const qcCanvas = document.getElementById('quantumCanvas');
        const qcCtx = qcCanvas.getContext('2d');
        const qcData = document.getElementById('quantumData');
        let cnotCount = 0, hadamardCount = 0, entangleCount = 0, errorCount = 0, correctCount = 0;

        // Dynamics
        let time = 0;
        const allAtoms = helix.flatMap(pair => pair.xt.concat(pair.xa));

        // Camera and Input
        camera.position.z = 40;
        const moveSpeed = 0.5;
        document.addEventListener('keydown', (e) => {
            switch (e.key) {
                case 'ArrowUp': camera.position.y += moveSpeed; break;
                case 'ArrowDown': camera.position.y -= moveSpeed; break;
                case 'ArrowLeft': camera.position.x -= moveSpeed; break;
                case 'ArrowRight': camera.position.x += moveSpeed; break;
                case 'Enter': scene.add(quantumGroup); break;
            }
        });
        document.addEventListener('keyup', (e) => {
            if (e.key === 'Enter') scene.remove(quantumGroup);
            if (e.key === ' ') {
                scene.remove(legendSprite);
                document.getElementById('info').style.display = 'block';
            }
        });
        const legendSprite = makeTextSprite("C: Gray, N: Blue, O: Red, F: Yellow, H: White, Synth: Purple, Entangle: Cyan, Correct: Green");
        document.addEventListener('keydown', (e) => {
            if (e.key === ' ') {
                scene.add(legendSprite);
                legendSprite.position.set(camera.position.x - 5, camera.position.y + 5, camera.position.z - 10);
                document.getElementById('info').style.display = 'none';
            }
        });

        // Animation
        function animate() {
            requestAnimationFrame(animate);
            time += 0.05;

            // Polymerase Motion
            const polyZ = ((time % 8) / 8) * 34;
            polymerase.position.z = polyZ;

            // Vibration
            allAtoms.forEach((atom, i) => {
                const amp = atom.userData.isFluorine ? 0.1 : 0.2;
                atom.position.x = atom.userData.basePos.x + Math.sin(time + i) * amp;
                atom.position.y = atom.userData.basePos.y + Math.cos(time + i) * amp;
            });

            // Staggered Replication
            const cycle = (time % 8) / 8;
            helix.forEach((pair, i) => {
                const zDist = Math.abs(polyZ - (i * 3.4));
                const offset = zDist < 5 ? (5 - zDist) : 0;
                pair.xt.forEach(atom => atom.position.x = atom.userData.basePos.x - offset);
                pair.xa.forEach(atom => atom.position.x = atom.userData.basePos.x + offset);
                hBonds[i * 2].geometry.setFromPoints([pair.xt[5].position, pair.xa[0].position]);
                hBonds[i * 2 + 1].geometry.setFromPoints([pair.xt[3].position, pair.xa[8].position]);
                hBonds[i * 2].computeLineDistances();
                hBonds[i * 2 + 1].computeLineDistances();
            });

            // Synthesis
            if (cycle < 0.1 && synthHelix.length < 10) {
                const z = synthHelix.length * 3.4;
                const angle = (synthHelix.length * 36) * Math.PI / 180;
                const xtAtoms = xtTemplate.map(([x, y, zBase, mat]) => {
                    const atom = new THREE.Mesh(atomGeo, synthMat);
                    const newX = (x - 5) * Math.cos(angle) - y * Math.sin(angle);
                    const newY = (x - 5) * Math.sin(angle) + y * Math.cos(angle);
                    atom.position.set(newX, newY, z);
                    atom.userData = { basePos: atom.position.clone(), isFluorine: mat === fluorineMat, opacity: 0 };
                    scene.add(atom);
                    return atom;
                });
                const xaAtoms = xaTemplate.map(([x, y, zBase, mat]) => {
                    const atom = new THREE.Mesh(atomGeo, synthMat);
                    const newX = (x + 5) * Math.cos(angle) - y * Math.sin(angle);
                    const newY = (x + 5) * Math.sin(angle) + y * Math.cos(angle);
                    atom.position.set(newX, newY, z);
                    atom.userData = { basePos: atom.position.clone(), isFluorine: mat === fluorineMat, opacity: 0 };
                    scene.add(atom);
                    return atom;
                });
                synthHelix.push({ xt: xtAtoms, xa: xaAtoms });
                const synthBonds = [[xtAtoms[5], xaAtoms[0]], [xtAtoms[3], xaAtoms[8]]].map(([a1, a2]) => {
                    const hBond = new THREE.Line(new THREE.BufferGeometry().setFromPoints([a1.position, a2.position]), hBondMat);
                    hBond.computeLineDistances();
                    scene.add(hBond);
                    return hBond;
                });
                synthPairs.push(synthBonds);
            }
            synthHelix.forEach((pair, i) => {
                pair.xt.concat(pair.xa).forEach(atom => {
                    atom.userData.opacity = Math.min(1, atom.userData.opacity + 0.05);
                    atom.material.opacity = atom.userData.opacity;
                });
                synthPairs[i].forEach(hBond => {
                    hBond.geometry.setFromPoints([pair.xt[5].position, pair.xa[0].position]);
                    hBond.computeLineDistances();
                });
            });
            if (cycle > 0.9) {
                synthHelix.forEach(pair => pair.xt.concat(pair.xa).forEach(atom => scene.remove(atom)));
                synthPairs.forEach(bonds => bonds.forEach(hBond => scene.remove(hBond)));
                synthHelix.length = 0;
                synthPairs.length = 0;
            }

            // Effects
            helixWireframe1.material.color.setHSL(time % 1, 1, 0.5);
            helixWireframe2.material.color.setHSL(time % 1, 1, 0.5);
            particles.children.forEach(p => scene.remove(p));
            particles.children = [];
            helix.forEach((pair, i) => {
                if (Math.abs(polyZ - (i * 3.4)) < 0.5) {
                    const particle = new THREE.Mesh(new THREE.SphereGeometry(0.1, 16, 16), new THREE.MeshBasicMaterial({ color: 0xffffff }));
                    particle.position.copy(pair.xt[5].position);
                    particles.add(particle);
                    cnotCount++;
                }
                if (Math.abs(polyZ - (i * 3.4)) > 4) {
                    const particle = new THREE.Mesh(new THREE.SphereGeometry(0.15, 16, 16), new THREE.MeshBasicMaterial({ color: 0xff0000 }));
                    particle.position.copy(pair.xa[0].position);
                    particles.add(particle);
                }
            });
            synthHelix.forEach((pair, i) => {
                if (pair.xt[0].userData.opacity < 0.5) {
                    const particle = new THREE.Mesh(new THREE.SphereGeometry(0.1, 16, 16), new THREE.MeshBasicMaterial({ color: 0x800080 }));
                    particle.position.copy(pair.xt[0].position);
                    particles.add(particle);
                }
            });
            allAtoms.concat(synthHelix.flatMap(p => p.xt.concat(p.xa))).forEach(atom => {
                const glow = new THREE.Mesh(glowGeo, glowMat);
                glow.position.copy(atom.position);
                glow.scale.setScalar(1 + Math.sin(time) * 0.2);
                particles.add(glow);
                if (atom.userData.isFluorine) hadamardCount++;
            });

            // Quantum Enhancements
            const entangleLines = [];
            helix.forEach((pair, i) => {
                if (pair.xt[5].position.distanceTo(pair.xa[0].position) < 3 && synthHelix[i]) {
                    const line = new THREE.Line(new THREE.BufferGeometry().setFromPoints([pair.xt[5].position, synthHelix[i].xt[5].position]), entangleMat);
                    entangleLines.push(line);
                    scene.add(line);
                    entangleCount++;
                }
            });
            const errorNodes = [];
            helix.forEach((pair, i) => {
                if (Math.random() < 0.01) { // 1% error rate
                    errorCount++;
                    const node = new THREE.Mesh(correctGeo, correctMat);
                    node.position.set(pair.xt[0].position.x + Math.sin(time) * 2, pair.xt[0].position.y + Math.cos(time) * 2, pair.xt[0].position.z);
                    errorNodes.push(node);
                    scene.add(node);
                    correctCount++;
                }
            });

            // Quantum Circuit
            qcCtx.clearRect(0, 0, qcCanvas.width, qcCanvas.height);
            for (let i = 0; i < 10; i++) {
                qcCtx.beginPath();
                qcCtx.arc(30 + i * 25, 50, 10, 0, 2 * Math.PI);
                qcCtx.fillStyle = helix[i].xt[5].position.distanceTo(helix[i].xa[0].position) < 3 ? 'cyan' : 'gray';
                qcCtx.fill();
                if (i < 9) {
                    qcCtx.moveTo(40 + i * 25, 50);
                    qcCtx.lineTo(55 + i * 25, 50);
                    qcCtx.strokeStyle = 'white';
                    qcCtx.stroke();
                }
                if (Math.abs(polyZ - (i * 3.4)) < 0.5) { // CNOT
                    qcCtx.fillStyle = 'yellow';
                    qcCtx.fillRect(25 + i * 25, 60, 10, 10);
                }
                qcCtx.fillStyle = 'green';
                qcCtx.fillRect(25 + i * 25, 80, 10 * Math.sin(time), 10); // Hadamard
                if (synthHelix[i]) { // Entanglement
                    qcCtx.strokeStyle = 'cyan';
                    qcCtx.beginPath();
                    qcCtx.moveTo(30 + i * 25, 50);
                    qcCtx.lineTo(30 + i * 25, 150);
                    qcCtx.stroke();
                }
            }
            qcData.textContent = `Qubit States: ${helix.map(p => p.xt[5].position.distanceTo(p.xa[0].position) < 3 ? '|1⟩' : '|0⟩').join(' ')}\n` +
                                 `CNOT: ${cnotCount}\nHadamard: ${hadamardCount}\nEntangled Pairs: ${entangleCount}\nError Rate: ${errorCount} flips\nCorrections: ${correctCount}`;

            // Data Orbit
            bondSprite.position.set(helix[0].xt[0].position.x - 5, helix[0].xt[0].position.y + 5, helix[0].xt[0].position.z);
            vibSprite.position.set(helix[0].xt[0].position.x - 5, helix[0].xt[0].position.y + 4, helix[0].xt[0].position.z);
            synthSprite.position.set(helix[0].xt[0].position.x - 5, helix[0].xt[0].position.y + 3, helix[0].xt[0].position.z);

            controls.update();
            renderer.render(scene, camera);
        }
        animate();

        document.getElementById('info').textContent = "Press Space for Legend, Enter for Quantum";
    </script>
</body>
</html>
