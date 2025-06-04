# layout-drawing-tool
layout for sheds that works from a PDF<!DOCTYPE html>
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
    let startX = 0, startY = 0;

    function setMode(newMode) {
      mode = newMode;
    }

    canvas.addEventListener('mousedown', (e) => {
      const x = e.offsetX;
      const y = e.offsetY;

      if (mode === 'draw' || mode === 'wall') {
        drawing = true;
        ctx.beginPath();
        ctx.moveTo(x, y);
      } else if (mode === 'window') {
        ctx.fillStyle = 'blue';
        ctx.fillRect(x - 20, y - 10, 40, 20);
      } else if (mode === 'door') {
        ctx.fillStyle = 'brown';
        ctx.fillRect(x - 15, y - 30, 30, 60);
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
        ctx.beginPath(); // reset
      }
    });

    function clearCanvas() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
    }

    function saveDrawing() {
      const imgData = canvas.toDataURL('image/png');
      const link = document.createElement('a');
      link.href = imgData;
      link.download = 'layout.png';
      link.click();
    }
  </script>
</body>
</html>

