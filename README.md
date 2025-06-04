

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
    <button onclick="setMode('line')">Straight Line</button>
    <button onclick="setMode('window')">Window (W)</button>
    <button onclick="setMode('sdoor')">Single Door (SD)</button>
    <button onclick="setMode('ddoor')">Double Door (DD)</button>
    <button onclick="setMode('outlet')">Electrical Outlet (EO)</button>
    <button onclick="setMode('label')">Add Label</button>
    <button onclick="clearCanvas()">Clear</button>
    <button onclick="saveDrawing()">Save Drawing</button>
  </div>

  <script>
    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d');
    let mode = 'draw';
    let drawing = false;
    let startX = 0;
    let startY = 0;

    function setMode(newMode) {
      mode = newMode;
    }

    function drawBuildingOutline() {
      ctx.strokeStyle = 'gray';
      ctx.lineWidth = 4;
      ctx.strokeRect(50, 50, 500, 300);
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
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    }

    function drawLabeledCircle(x, y, radius, color, label, textColor = 'white') {
      ctx.beginPath();
      ctx.fillStyle = color;
      ctx.arc(x, y, radius, 0, 2 * Math.PI);
      ctx.fill();
      ctx.strokeStyle = 'black';
      ctx.stroke();
      ctx.fillStyle = textColor;
      ctx.font = 'bold 12px Arial';
      ctx.textAlign = 'center';
      ctx.textBaseline = 'middle';
      ctx.fillText(label, x, y);
    }

    canvas.addEventListener('mousedown', (e) => {
      startX = e.offsetX;
      startY = e.offsetY;

      if (mode === 'draw' || mode === 'wall') {
        drawing = true;
        ctx.beginPath();
        ctx.moveTo(startX, startY);
      } else if (mode === 'line') {
        drawing = true;
      } else if (mode === 'window') {
        drawLabeledCircle(startX, startY, 15, 'blue', 'W');
      } else if (mode === 'sdoor') {
        drawLabeledCircle(startX, startY, 18, 'brown', 'SD');
      } else if (mode === 'ddoor') {
        drawLabeledCircle(startX, startY, 20, 'brown', 'DD');
      } else if (mode === 'outlet') {
        drawLabeledCircle(startX, startY, 8, 'yellow', 'EO', 'black');
      } else if (mode === 'label') {
        const text = prompt("Enter label text:");
        if (text) {
          ctx.font = '16px Arial';
          ctx.fillStyle = 'black';
          ctx.fillText(text, startX, startY);
        }
      }
    });

    canvas.addEventListener('mouseup', (e) => {
      if (drawing && mode === 'line') {
        const endX = e.offsetX;
        const endY = e.offsetY;
        ctx.beginPath();
        ctx.lineWidth = 3;
        ctx.strokeStyle = 'black';

        const dx = Math.abs(endX - startX);
        const dy = Math.abs(endY - startY);

        if (dx > dy) {
          // Horizontal line
          ctx.moveTo(startX, startY);
          ctx.lineTo(endX, startY);
        } else {
          // Vertical line
          ctx.moveTo(startX, startY);
          ctx.lineTo(startX, endY);
        }
        ctx.stroke();
      }

      if (drawing && (mode === 'draw' || mode === 'wall')) {
        drawing = false;
        ctx.beginPath();
      }

      if (drawing && mode === 'line') {
        drawing = false;
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

    window.onload = drawBuildingOutline;
  </script>
</body>
</html>
