<!DOCTYPE html>
<html lang="ro">
<head>
  <meta charset="UTF-8" />
  <title>Control Photo</title>
  <style>
    html, body {
      margin: 0; padding: 0; height: 100%;
      background: #111;
      display: flex; justify-content: center; align-items: center;
      font-family: Arial, sans-serif;
      color: white;
      overflow: hidden;
    }
    button {
      width: 250px; height: 250px;
      border-radius: 50%;
      border: none;
      background-color: #28a745;
      color: white;
      font-size: 6em;
      cursor: pointer;
      user-select: none;
      display: flex;
      justify-content: center;
      align-items: center;
      transition: background-color 0.3s ease;
    }
    button.red {
      background-color: #dc3545;
    }
    #flash {
      position: fixed;
      top: 0; left: 0; right: 0; bottom: 0;
      background: white;
      opacity: 0;
      pointer-events: none;
      transition: opacity 0.15s ease;
      z-index: 1000;
    }
  </style>
</head>
<body>

<button id="shootBtn">📸</button>
<div id="flash"></div>

<script>
  const button = document.getElementById('shootBtn');
  const flash = document.getElementById('flash');
  const laptopIP = '192.168.1.100'; // schimba cu IP-ul laptopului tau
  const port = 3000; // portul serverului tau

  async function countdown(seconds) {
    for(let i = seconds; i > 0; i--) {
      button.textContent = i;
      await new Promise(res => setTimeout(res, 1000));
    }
    button.textContent = '📸';
  }

  function flashEffect() {
    flash.style.opacity = '1';
    setTimeout(() => {
      flash.style.opacity = '0';
    }, 150);
  }

  button.addEventListener('click', async () => {
    button.disabled = true;
    button.classList.add('red');
    await countdown(3);
    flashEffect();

    fetch(`http://${laptopIP}:${port}/shoot`, { method: 'POST' })
      .then(res => res.json())
      .then(data => {
        console.log('Foto făcută!', data);
      })
      .catch(err => {
        console.error('Eroare la conectare:', err);
      })
      .finally(() => {
        button.classList.remove('red');
        button.disabled = false;
        button.textContent = '📸';
      });
  });
</script>

</body>
</html>
