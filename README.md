<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="utf-8">
<title>Démo POV VR - HUD conducteur (maquette)</title>
<script src="https://aframe.io/releases/1.5.0/aframe.min.js"></script>
<style>
  body{margin:0;font-family:sans-serif}
  #banner{
    position:fixed;top:10px;left:10px;right:10px;
    background:rgba(0,0,0,.75);color:#fff;
    padding:10px 16px;border-radius:8px;font-size:13px;z-index:999;
    text-align:center;
  }
</style>
</head>
<body>

<div id="banner">🎬 Démo visuelle / maquette — non conçue pour être utilisée en conduisant</div>

<a-scene fog="type: exponential; color: #0a0e12; density: 0.08">

  <a-assets>
    <a-mixin id="hudpanel" material="color:#0B131A; opacity:0.55; shader:flat; side:double"></a-mixin>
  </a-assets>

  <!-- Ciel nocturne -->
  <a-sky color="#05070a"></a-sky>

  <!-- ===================== -->
  <!-- ROUTE QUI DÉFILE (POV) -->
  <!-- ===================== -->
  <a-entity id="road-rig" position="0 0 0">

    <!-- Chaussée -->
    <a-plane rotation="-90 0 0" width="14" height="400" position="0 0 -190"
      material="color:#181c20; shader:flat"></a-plane>

    <!-- Bas-côtés -->
    <a-plane rotation="-90 0 0" width="40" height="400" position="-27 -0.02 -190"
      material="color:#0d1f14; shader:flat"></a-plane>
    <a-plane rotation="-90 0 0" width="40" height="400" position="27 -0.02 -190"
      material="color:#0d1f14; shader:flat"></a-plane>

    <!-- Marquage au sol : entité qui bouclera vers l'avant en JS -->
    <a-entity id="lane-lines"></a-entity>

    <!-- Quelques éléments de décor qui défilent -->
    <a-entity id="scenery"></a-entity>

  </a-entity>

  <!-- ===================== -->
  <!-- HUD FLOTTANT ANCRÉ CAMÉRA -->
  <!-- ===================== -->
  <a-entity camera look-controls wasd-controls position="0 1.6 0">

    <!-- Compteur de vitesse en bas à gauche du champ de vision -->
    <a-entity position="-0.55 -0.35 -1" rotation="0 12 0">
      <a-circle radius="0.16" mixin="hudpanel"></a-circle>
      <a-circle radius="0.16" material="color:#FF3B30; shader:flat; opacity:0.9" side="double"
        geometry="primitive:ring; radiusInner:0.145; radiusOuter:0.16"></a-circle>
      <a-text value="102" align="center" color="#FF9D2E" width="1.4" position="0 0.01 0.01"></a-text>
      <a-text value="km/h" align="center" color="#ffffff" width="0.9" position="0 -0.06 0.01"></a-text>
    </a-entity>

    <!-- Limite de vitesse -->
    <a-entity position="-0.32 -0.28 -1">
      <a-circle radius="0.055" material="color:#ffffff; shader:flat"></a-circle>
      <a-circle radius="0.055" material="color:#FF3B30; shader:flat" side="double"
        geometry="primitive:ring; radiusInner:0.048; radiusOuter:0.055"></a-circle>
      <a-text value="100" align="center" color="#C0271F" width="1.1" position="0 0 0.01"></a-text>
    </a-entity>

    <!-- Navigation en haut -->
    <a-entity position="0 0.45 -1">
      <a-plane width="0.5" height="0.13" mixin="hudpanel"></a-plane>
      <a-text value="↑ 2 km" align="center" color="#3FE0CE" width="1.3" position="0 0 0.01"></a-text>
    </a-entity>

    <!-- Radar circulaire en bas à droite -->
    <a-entity id="radar" position="0.55 -0.35 -1" rotation="0 -12 0">
      <a-circle radius="0.18" material="color:#0B131A; opacity:0.6; shader:flat"></a-circle>
      <a-circle id="radar-sweep" radius="0.17"
        material="color:#3FE0CE; opacity:0.25; shader:flat"
        geometry="primitive:circle; radius:0.17; thetaLength:70"></a-circle>
      <a-circle radius="0.17" material="color:#233140; shader:flat" side="double"
        geometry="primitive:ring; radiusInner:0.165; radiusOuter:0.17"></a-circle>

      <!-- points détectés -->
      <a-circle radius="0.012" color="#FF3B30" position="0.09 0.06 0.01"></a-circle>
      <a-circle radius="0.011" color="#F2B33A" position="-0.05 -0.08 0.01"></a-circle>
      <a-circle radius="0.008" color="#9FB0BE" position="0.01 0.1 0.01"></a-circle>

      <a-text value="3 détectés" align="center" color="#3FE0CE" width="1" position="0 -0.22 0.01"></a-text>
    </a-entity>

    <!-- Indicateur REC / caméra (juste décoratif) -->
    <a-entity position="-0.7 0.48 -1">
      <a-circle id="rec-dot" radius="0.018" color="#FF3B30"></a-circle>
    </a-entity>

  </a-entity>

</a-scene>

<script>
  // Défilement de la route : on avance la caméra doucement (sensation immersive)
  // et on fait boucler des lignes / éléments de décor vers l'avant.
  AFRAME.registerComponent('scroll-road', {
    init: function () {
      this.speed = 6; // unités / seconde
      this.lines = [];
      this.scenery = [];
      const lineContainer = document.querySelector('#lane-lines');
      const sceneryContainer = document.querySelector('#scenery');

      // Lignes centrales pointillées
      for (let i = 0; i < 20; i++) {
        const line = document.createElement('a-plane');
        line.setAttribute('width', 0.25);
        line.setAttribute('height', 3);
        line.setAttribute('rotation', '-90 0 0');
        line.setAttribute('position', `0 0.01 ${-i * 10}`);
        line.setAttribute('material', 'color:#ffffff; shader:flat');
        lineContainer.appendChild(line);
        this.lines.push(line);
      }

      // Poteaux / bornes décoratives de chaque côté
      for (let i = 0; i < 25; i++) {
        const side = i % 2 === 0 ? -7.5 : 7.5;
        const pole = document.createElement('a-box');
        pole.setAttribute('width', 0.15);
        pole.setAttribute('height', 1.4);
        pole.setAttribute('depth', 0.15);
        pole.setAttribute('position', `${side} 0.7 ${-i * 16}`);
        pole.setAttribute('material', 'color:#2a2f35; shader:flat');
        sceneryContainer.appendChild(pole);
        this.scenery.push(pole);
      }
    },
    tick: function (time, delta) {
      const dz = (this.speed * delta) / 1000;
      this.lines.forEach(line => {
        let z = parseFloat(line.getAttribute('position').z) + dz;
        if (z > 5) z -= 200;
        line.setAttribute('position', `0 0.01 ${z}`);
      });
      this.scenery.forEach(pole => {
        let pos = pole.getAttribute('position');
        let z = pos.z + dz;
        if (z > 5) z -= 400;
        pole.setAttribute('position', `${pos.x} ${pos.y} ${z}`);
      });
    }
  });
  document.querySelector('a-scene').setAttribute('scroll-road', '');

  // Rotation du secteur radar
  AFRAME.registerComponent('radar-spin', {
    tick: function (time) {
      this.el.setAttribute('rotation', `0 0 ${-(time / 8) % 360}`);
    }
  });
  document.querySelector('#radar-sweep').setAttribute('radar-spin', '');

  // Clignotement du point REC
  let recVisible = true;
  setInterval(() => {
    recVisible = !recVisible;
    document.querySelector('#rec-dot').setAttribute('visible', recVisible);
  }, 650);
</script>

</body>
</html>
