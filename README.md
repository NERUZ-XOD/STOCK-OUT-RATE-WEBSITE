# Stock Out Rate Calculator

## Overview
The Stock Out Rate Calculator is a web-based tool designed to help users calculate stock out rates for inventory management. It provides two main functionalities:

- **Single-Day Stock Out Rate**: Calculates the stock out rate for a single day based on stock outs and total demand.
- **Average Stock Out Rate**: Calculates the average stock out rate over a selected month by entering daily rates.

This README focuses on the HTML structure of the website, which serves as the foundation for the user interface. The project includes a CSS file for styling, but currently lacks a JavaScript file for interactivity. Adding JavaScript is necessary to enable the calculator’s full functionality.

## Features
- User-friendly interface for entering stock out data.
- Option to calculate a single-day stock out rate or an average rate over a month (when JavaScript is added).
- Displays results and maintains a calculation history for single-day rates (with JavaScript).
- Includes a navigation bar, calculator sections, and a footer with social links.
- Styled with a CSS file for a dark-themed, modern appearance.

## Project Structure
The project consists of the following files:

- `index.html`: The main HTML file containing the website structure.
- `styles.css`: The CSS file for styling the website (included in your project).
- `script.js`: Not currently included, but required for interactivity (see "Adding JavaScript" section below).

## Installation

### Clone or Download the Repository
If hosted in a repository, clone it to your local machine:

```bash
git clone https://github.com/NERUZ-XOD/STOCK-OUT-RATE-WEBSITE
```

Otherwise, download the `index.html` and `styles.css` files directly.

### Open in a Web Browser
- Open the `index.html` file in any modern web browser (e.g., Chrome, Firefox, Safari) to view the static structure and styling.
- **Note**: Without JavaScript, the calculator will not be interactive (e.g., buttons won’t work, and calculations won’t occur).

### Add JavaScript (Optional)
To enable full functionality, create a `script.js` file with the JavaScript code provided below (or as generated in a previous response) and link it to your `index.html` file.

## Usage

### Viewing the Static HTML with CSS
- Open `index.html` in a web browser to see the basic structure and styling of the Stock Out Rate Calculator.
- The page includes:
  - A navigation bar with buttons for switching between single-day and average rate calculators.
  - Input fields for entering stock out data (e.g., stock outs, total demand, or daily rates for a month).
  - Result displays, a history section for single-day calculations, and a results table for average rates.
  - A footer with copyright information and social media links.
- The CSS file (`styles.css`) provides a dark-themed design with green accents, as shown in your screenshot.

### Adding JavaScript for Interactivity
To make the calculator fully functional, add a JavaScript file (`script.js`) with the following code and link it to your `index.html` file:

```html
<script src="script.js"></script>
```

Here’s the JavaScript code to save as `script.js`:

```javascript
function showSection(section) {
    document.getElementById('averageRate').classList.add('hidden');
    document.getElementById('singleDayRate').classList.add('hidden');
    document.getElementById(section).classList.remove('hidden');
}

// Days in each month (assuming non-leap year for February)
const daysInMonth = { 
    January: 31, 
    February: 28, 
    March: 31, 
    April: 30, 
    May: 31, 
    June: 30, 
    July: 31, 
    August: 31, 
    September: 30, 
    October: 31, 
    November: 30, 
    December: 31 
};

let rates = [];
let currentDay = 1;

// Reset input fields and data for average rate calculation
function resetInput() {
    rates = [];
    currentDay = 1;
    document.getElementById('resultsTable').innerHTML = '';
    enterDailyRates();
}

// Function to handle daily rate input for average calculation
function enterDailyRates() {
    const totalDays = daysInMonth[document.getElementById('month').value];
    if (currentDay > totalDays) {
        document.getElementById('rateInput').innerHTML = '<p>Month complete. Please reset to enter new data.</p>';
        return;
    }
    
    const rateInput = document.getElementById('rateInput');
    rateInput.innerHTML = `
        <div class="input-group">
            <label>Enter rate for Day ${currentDay}</label>
            <input type='number' id='dayRate'>
        </div>`;
    
    let resetButton = document.createElement('button');
    resetButton.innerText = 'Reset';
    resetButton.onclick = resetInput;
    
    let backButton = document.createElement('button');
    backButton.innerText = 'Back';
    backButton.onclick = function() {
        if (currentDay > 1) {
            rates.pop();
            currentDay--;
            enterDailyRates();
            updateTable();
        }
    };
    
    let nextButton = document.createElement('button');
    nextButton.innerText = currentDay < totalDays ? 'Next' : 'Submit';
    nextButton.onclick = function() {
        submitDailyRate(totalDays);
    };
    
    rateInput.appendChild(resetButton);
    rateInput.appendChild(backButton);
    rateInput.appendChild(nextButton);
    
    let inputField = document.getElementById('dayRate');
    inputField.focus();
    inputField.addEventListener('keypress', function(event) {
        if (event.key === 'Enter') {
            submitDailyRate(totalDays);
        }
    });
}

// Function to submit daily rate and proceed to next day or finalize
function submitDailyRate(totalDays) {
    let inputField = document.getElementById('dayRate');
    if (!inputField.value) {
        alert('Please enter a value!');
        return;
    }
    rates.push(parseFloat(inputField.value));
    
    if (currentDay < totalDays) {
        currentDay++;
        enterDailyRates();
        updateTable();
    } else {
        updateTable();
        enterDailyRates(); // Show completion message
    }
}

// Function to update the results table for average rate calculation
function updateTable() {
    const month = document.getElementById('month').value;
    const totalDays = daysInMonth[month];
    const tableDiv = document.getElementById('resultsTable');
    let tableHTML = `
        <table>
            <tr>
                <th>Date</th>
                <th>Rate</th>
            </tr>`;
    
    for (let i = 0; i < Math.min(rates.length, totalDays); i++) {
        tableHTML += `
            <tr>
                <td>${month} ${i + 1}</td>
                <td>${rates[i].toFixed(2)}</td>
            </tr>`;
    }
    
    if (rates.length > 0) {
        const average = rates.reduce((a, b) => a + b, 0) / rates.length;
        tableHTML += `
            <tr>
                <td>Average</td>
                <td>${average.toFixed(2)}</td>
            </tr>`;
    }
    
    tableHTML += `</table>`;
    tableDiv.innerHTML = tableHTML;
}

// Function to calculate single-day stock out rate
function calculateSingleDayRate() {
    let stockOuts = parseFloat(document.getElementById('stockOuts').value);
    let totalDemand = parseFloat(document.getElementById('totalDemand').value);
    
    if (!stockOuts || !totalDemand || totalDemand === 0) {
        alert('Please enter valid values!');
        return;
    }
    
    let rate = (stockOuts / totalDemand) * 100;
    document.getElementById('singleDayResult').innerText = 'Stock Out Rate: ' + rate.toFixed(2) + '%';
    
    let historyList = document.getElementById('history');
    let listItem = document.createElement('li');
    listItem.innerText = `Stock Outs: ${stockOuts}, Total Demand: ${totalDemand}, Rate: ${rate.toFixed(2)}%`;
    historyList.appendChild(listItem);
}

// Add Enter key event listeners for single-day inputs
document.getElementById('stockOuts').addEventListener('keypress', function(event) {
    if (event.key === 'Enter') {
        calculateSingleDayRate();
    }
});

document.getElementById('totalDemand').addEventListener('keypress', function(event) {
    if (event.key === 'Enter') {
        calculateSingleDayRate();
    }
});

// Initialize the average rate calculator when the page loads
window.onload = resetInput;
```

This JavaScript enables:
- Switching between the single-day and average rate calculators via navigation buttons.
- Calculating the single-day stock out rate based on user input.
- Entering and calculating average stock out rates for a selected month, including navigation (Next, Back, Reset) and displaying results in a table.

## Example Interaction (with JavaScript)
- **Single-Day Stock Out Rate**: Enter the number of stock outs and total demand, then click "Calculate" to see the percentage rate and add it to the history.
- **Average Stock Out Rate**: Select a month, enter daily stock out rates, use "Next" to proceed, "Back" to revise, or "Reset" to start over, and view the average rate in a table.

## Contributing
If you’d like to contribute to this project:

1. Fork the repository.
2. Create a new branch for your feature or bug fix.
3. Make your changes (e.g., update HTML, CSS, or add JavaScript) and submit a pull request with a description of your changes.

## License
This project is licensed under the [MIT License](LICENSE) - see the `LICENSE` file for details.

## Contact
- **Author**: Neeraj M
- **Email**: neruz4life@gmail.com
- **Social Links**:
  - X: [https://x.com/NeruzXod7322](https://x.com/NeruzXod7322)
  - LinkedIn: [https://www.linkedin.com/in/neerajm2007/](https://www.linkedin.com/in/neerajm2007/)
  - GitHub: [https://github.com/NERUZ-XOD](https://github.com/NERUZ-XOD)

## Acknowledgments
- Inspired by inventory management needs for tracking stock out rates.
- Built using standard HTML and styled with CSS, with optional JavaScript for enhanced functionality.
```

