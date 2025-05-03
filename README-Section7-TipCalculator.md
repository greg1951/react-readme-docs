# Tip Calculator Challenge Exercise
This exercise was at the end of Section 7 and it was indeed challenging. It was developed in the CodeSandbox. It has been saved to a private GitHub repo, FYI.

## The Challenge
Build an app that calculates a restaurant tip amount based on the check amount and 2 service ratings. Each of the service ratings are based on percentages derviced from a common selection list. 

![Tip Calculator UI](readme-screenshots/sections1-7/tip-calculator-challenge.png)

The solution should employ good uses of:
* A common component for the service rating selection
* Implement children props to make the common component more generic and reusable
* Component for entering of the check amount
* Component to display the total amount based on the check and tip amount average
* The total amount component should not be displayed when the app is first loaded or reset.
* A component to reset the form state

## Solution
```javascript
import "./styles.css";
import { useState } from "react";

export default function App() {
  return (
    <div>
      <TipCalculator />
    </div>
  );
  /*
  Created this high-level component so the imput fields would behave properly.
  (I was losing the cursor on every character entered in the check amount field.)
*/
  function TipCalculator() {
    const [billAmount, setBillAmount] = useState("");
    const [party1Pct, setParty1Pct] = useState(-1);
    const [party2Pct, setParty2Pct] = useState(-1);

    const tip = billAmount * ((party1Pct + party2Pct) / 2 / 100);

    return (
      <div className="App">
        <BillAmount billAmount={billAmount} onBillChange={setBillAmount} />
        <SelectTipPercentage percent={party1Pct} onSelect={setParty1Pct}>
          {"How did you like the service?"}
        </SelectTipPercentage>
        <SelectTipPercentage percent={party2Pct} onSelect={setParty2Pct}>
          {"How did your friend like the service?"}
        </SelectTipPercentage>
        {billAmount > 0 && (
          <>
            <FinalPayment billAmount={billAmount} tip={tip} />
            <Reset onReset={handleReset} />
          </>
        )}
      </div>
    );
    function handleReset() {
      setParty1Pct(-1);
      setParty2Pct(-1);
      setBillAmount("");
    }
  }

  function BillAmount({ billAmount, onBillChange }) {
    return (
      <div>
        <label>What is the check amount?</label>
        <input
          type="number"
          placeholder="Bill Amount"
          value={billAmount}
          onChange={(e) => onBillChange(Number(e.target.value))}
        />
      </div>
    );
  }
  function SelectTipPercentage({ children, percent, onSelect }) {
    return (
      <div>
        <label>{children}</label>
        <select required
          value={percent}
          onChange={(e) => onSelect(Number(e.target.value))}
        >
          <option value="-1">Please rate the service</option>
          <option value="0">HORRIBLE</option>
          <option value="5">NOT GOOD</option>
          <option value="10">OK</option>
          <option value="15">GOOD</option>
          <option value="20">RAD</option>
        </select>
      </div>
    );
  }
  function FinalPayment({ billAmount, tip }) {
    const numTotal = billAmount + tip;
    const fmtdTotal = numTotal.toFixed(2);
    return (
      <div>
        <h4>
          Pay total: ${fmtdTotal} (Check: ${billAmount} + Tip: ${tip})
        </h4>
      </div>
    );
  }
  function Reset({ onReset }) {
    return <button onClick={onReset}>Reset</button>;
  }
}
```
## Style Sheet
The CSS style sheet was very simple, basically to align the input fields and labels.
```css
.App {
  font-family: sans-serif;
  text-align: left;
}

form {
  vertical-align: text-bottom;
  text-align: left;
}
label {
  width: 350px;
  display: inline-block;
}
```

## Interesting Observations
* The App function implements a top level **TipCalculator** component that is the parent to all of the other components. 
    * Initially I had not implemented TipCalculator and was running the other components from the App function. 
    * However, the input for the **billAmount** variable however did not behave correctly: *the cursor left the input field each time the onChange event ran*.
* Each of the input components (BillAmount, SelectTipPercentage) contained input fields, without a form element. Initially I had a form associated with each but it was not necessary.

* When I was designing what I thought would be a good implementation of children props I was focusing on using children to render the selection list. I did peek at the final solution and saw it was simply for the label for the percentage selection.

* Initially I created handlers for the selection list onSelect event. However, passing the state set method was very cool, as shown below.

```javascript
    return (
      <div className="App">
        <BillAmount billAmount={billAmount} onBillChange={setBillAmount} />
        <SelectTipPercentage percent={party1Pct} onSelect={setParty1Pct}>
          {"How did you like the service?"}
        </SelectTipPercentage>
        <SelectTipPercentage percent={party2Pct} onSelect={setParty2Pct}>
          {"How did your friend like the service?"}
        </SelectTipPercentage>
        ...
  function SelectTipPercentage({ children, percent, onSelect }) {
    return (
      <div>
        <label>{children}</label>
        <select
          value={percent}
          onChange={(e) => onSelect(Number(e.target.value))}
        >
          <option value="0">HORRIBLE</option>
          <option value="5">NOT GOOD</option>
          <option value="10">OK</option>
          <option value="15">GOOD</option>
          <option value="20">RAD</option>
        </select>
      </div>
    );
  }

```