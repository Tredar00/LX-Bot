# LX-Bot

<a href="https://lenx-signal.github.io/LX-Bot/">link</a>
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Bot 55 - EUR/USD Signal</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <h1>Bot 55 - EUR/USD Signal</h1>
  <button onclick="getSignal()">Start Operation</button>
  <div id="signalBox">Signal will appear here...</div>

  <script src="script.js"></script>
</body>
</html>
body {
  font-family: Arial, sans-serif;
  background-color: #f4f4f4;
  text-align: center;
  padding: 50px;
}

button {
  padding: 12px 25px;
  font-size: 20px;
  background-color: #007BFF;
  color: white;
  border: none;
  cursor: pointer;
}

#signalBox {
  margin-top: 30px;
  font-size: 26px;
  font-weight: bold;
}
async function getSignal() {
  const res = await fetch("https://api.exchangerate.host/timeseries?start_date=2024-07-01&end_date=2024-07-25&base=EUR&symbols=USD");
  const data = await res.json();
  const rates = Object.values(data.rates).map(day => day.USD);

  if (rates.length < 20) {
    document.getElementById("signalBox").innerText = "Not enough data.";
    return;
  }

  const closes = rates.slice(-20);
  const ma = average(closes.slice(-5));
  const rsi = calculateRSI(closes);

  const current = closes[closes.length - 1];
  let signal = "NO TRADE";

  if (rsi < 30 && current < ma) signal = "BUY (UP)";
  else if (rsi > 70 && current > ma) signal = "SELL (DOWN)";

  document.getElementById("signalBox").innerText = signal;
}

function average(arr) {
  return arr.reduce((a, b) => a + b) / arr.length;
}

function calculateRSI(closes, period = 14) {
  let gains = 0, losses = 0;
  for (let i = 1; i <= period; i++) {
    const diff = closes[closes.length - i] - closes[closes.length - i - 1];
    if (diff >= 0) gains += diff;
    else losses -= diff;
  }
  const rs = gains / (losses || 1);
  return 100 - (100 / (1 + rs));
}
