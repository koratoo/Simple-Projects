
![image](https://github.com/user-attachments/assets/25921640-76f2-4fb6-bb3c-5c85444e1259)


``` html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Calculator</title>
    <style>
      body {
        font-family: Arial, sans-serif;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        margin: 0;
        background-color: #f0f0f0;
      }
      .calculator {
        background: #fff;
        border-radius: 10px;
        box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
        padding: 20px;
        width: 300px;
      }
      .display {
        width: 100%;
        height: 50px;
        border: none;
        background: #f0f0f0;
        font-size: 1.5rem;
        text-align: right;
        padding: 0 10px;
        margin-bottom: 10px;
        border-radius: 5px;
      }
      .buttons {
        display: grid;
        grid-template-columns: repeat(4, 1fr);
        gap: 10px;
      }
      button {
        height: 50px;
        font-size: 1.2rem;
        border: none;
        border-radius: 5px;
        background: #007bff;
        color: white;
        cursor: pointer;
      }
      button:active {
        background: #0056b3;
      }
      .operator {
        background: #28a745;
      }
      .operator:active {
        background: #1e7e34;
      }
      .clear {
        background: #dc3545;
      }
      .clear:active {
        background: #c82333;
      }
    </style>
  </head>
  <body>
    <div class="calculator">
      <input type="text" class="display" id="display" disabled />
      <div class="buttons">
        <button onclick="appendNumber('7')">7</button>
        <button onclick="appendNumber('8')">8</button>
        <button onclick="appendNumber('9')">9</button>
        <button class="operator" onclick="appendOperator('/')">/</button>

        <button onclick="appendNumber('4')">4</button>
        <button onclick="appendNumber('5')">5</button>
        <button onclick="appendNumber('6')">6</button>
        <button class="operator" onclick="appendOperator('*')">*</button>

        <button onclick="appendNumber('1')">1</button>
        <button onclick="appendNumber('2')">2</button>
        <button onclick="appendNumber('3')">3</button>
        <button class="operator" onclick="appendOperator('-')">-</button>

        <button onclick="appendNumber('0')">0</button>
        <button onclick="appendNumber('.')">.</button>
        <button class="clear" onclick="clearDisplay()">C</button>
        <button class="operator" onclick="appendOperator('+')">+</button>

        <button
          class="operator"
          style="grid-column: span 4"
          onclick="calculate()"
        >
          =
        </button>
      </div>
    </div>

    <script>
      const display = document.getElementById("display");

      function appendNumber(number) {
        display.value += number;
      }

      function appendOperator(operator) {
        if (display.value === "" || isNaN(display.value.slice(-1))) return;
        display.value += operator;
      }

      function clearDisplay() {
        display.value = "";
      }

      function calculate() {
        try {
          display.value = eval(display.value);
        } catch (e) {
          alert("Invalid calculation");
          clearDisplay();
        }
      }
    </script>
  </body>
</html>
```
