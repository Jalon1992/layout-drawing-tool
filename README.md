<!DOCTYPE html>
<html>
<head>
  <title>Building Layout Tool</title>
  <style>
    canvas { border: 1px solid black; display: block; }
    .toolbar { margin-top: 10px; }
    button { margin: 5px; }
  </style>
</head>
<body>
  <h2>Draw Your Building Layout</h2>
  <canvas id="canvas" width="600" height="400"></canvas>

  <div class="toolbar">
    <button onclick="setMode('draw')">Freehand</button>
    <button onclick="setMode('wall')">Wall</button>
    <button onclick="setMode('window')">Add Window</button>
    <button onclick="setMode('door')">Add Door</button>
    <button onclick="setMode('label')">Add Label</button>
    <button onclick="clearCanvas()">Clear</button>
    <button onclick="saveDrawing()">Save Drawing</button>
  </div>

  <script>
    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d');
    let mode = 'draw';
    let drawing = false;

    function setMode(newMode) {
      mode = newMode;
    }

    function drawBuildingOutline() {
      ctx.strokeStyle = 'gray';
      ctx.lineWidth = 4;
      ctx.strokeRect(50, 50, 500, 300); // x, y, width, height
    }

    function clearCanvas() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      drawBuildingOutline();
    }

    function saveDrawing() {
      const imgData = canvas.toDataURL('image/png');
      const link = document.createElement('a');
      link.href = imgData;
      link.download = 'layout.png';
      link.click();
    }

    canvas.addEventListener('mousedown', (e) => {
      const x = e.offsetX;
      const y = e.offsetY;

      if (mode === 'draw' || mode === 'wall') {
        drawing = true;
        ctx.beginPath();
        ctx.moveTo(x, y);
      } else if (mode === 'window') {
        ctx.beginPath();
        ctx.fillStyle = 'blue';
        ctx.arc(x, y, 15, 0, 2 * Math.PI);
        ctx.fill();
      } else if (mode === 'door') {
        ctx.beginPath();
        ctx.fillStyle = 'brown';
        ctx.arc(x, y, 20, 0, 2 * Math.PI);
        ctx.fill();
      } else if (mode === 'label') {
        const text = prompt("Enter label text:");
        if (text) {
          ctx.font = '16px Arial';
          ctx.fillStyle = 'black';
          ctx.fillText(text, x, y);
        }
      }
    });

    canvas.addEventListener('mousemove', (e) => {
      if (drawing && (mode === 'draw' || mode === 'wall')) {
        ctx.lineWidth = mode === 'wall' ? 6 : 2;
        ctx.lineCap = 'round';
        ctx.strokeStyle = mode === 'wall' ? 'gray' : 'black';
        ctx.lineTo(e.offsetX, e.offsetY);
        ctx.stroke();
      }
    });

    canvas.addEventListener('mouseup', () => {
      if (drawing) {
        drawing = false;
        ctx.beginPath();
      }
    });

    window.onload = drawBuildingOutline;
  </script>
</body>
</html>


