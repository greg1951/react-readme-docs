1. [Currency Conversion](#currency-conversion)
2. [Setting State](#setting-state)
3. [The useEffect Code](#the-useeffect-code)
   
# Currency Conversion

This exercise reinforced the useEffect concepts, focusing on preventing potential race conditions, in the case of async API calls. 

What is different in this case, it was **not the use of an effect cancel function** but to simply **disable the input** if a request is in progress.

Here is a [link to the Code Sandbox project](https://codesandbox.io/p/github/greg1951/react-challenge-currency-converter-starter) exported into my GitHub account.

Here is the UI that performs the currency conversion.

![Here is the UI that performs the currency conversion.](readme-screenshots/section12/currency-conversion-screen.png)

The exercise requirements are listed below. 
* Provide two currency lists for the from/to selections
* An input amount field that when entered would be converted and shown below the form
* If any list selection is done, reconvert the input
* The from/to currencies cannot be the same selection (e.g. "USD" and "USD")
* The form fields should be disabled when a currency request was made. The fields should be reenabled when the request completes.

A stretch goal for the exercise would be to add error handling to the async currency conversion API.

# Setting State

Much of the state revolves around the form fields.

```javascript
export default function App() {
  const [amount, setAmount] = useState(1);
  const [fromCur, setFromCur] = useState("USD");
  const [toCur, setToCur] = useState("EUR");
  const [convertedAmt, setConvertedAmt] = useState("0");
  const [isLoading, setIsLoading] = useState(false);
```

The form is shown below, utilizing the above state.

```javascript
  return (
    <div>
      <input
        type="text"
        value={amount}
        disabled={isLoading}
        onChange={(e) => {
          setAmount(Number(e.target.value));
        }}
      />
      <select
        value={fromCur}
        disabled={isLoading}
        onChange={(e) => {
          setFromCur(e.target.value);
        }}
      >
        <option value="USD">USD</option>
        <option value="EUR">EUR</option>
        <option value="CAD">CAD</option>
        <option value="INR">INR</option>
      </select>
      <select
        value={toCur}
        disabled={isLoading}
        onChange={(e) => {
          setToCur(e.target.value);
        }}
      >
        <option value="USD">USD</option>
        <option value="EUR">EUR</option>
        <option value="CAD">CAD</option>
        <option value="INR">INR</option>
      </select>
      <p>
        {convertedAmt} [{toCur}]
      </p>
    </div>
  );
```
# The useEffect Code

Now, for the useEffect hook which include a try/catch block for error handling in the async API call.

```javascript
  useEffect(
    function () {
      try {
        async function convert() {
          // AbortController provided below for error handling
          const controller = new AbortController();
          setError("");

          setIsLoading(true);
          const response = await fetch(
            `https://api.frankfurter.app/latest?amount=${amount}&from=${fromCur}&to=${toCur}`,
            { signal: controller.signal }
          );

          if (!response.ok)
            throw new Error(
              "Something went wrong in the currency conversion API call"
            );

          const data = await response.json();

          if (data.Response === "False")
            throw new Error("Currency conversion response not valid");

          setConvertedAmt(data.rates[toCur]);
          setIsLoading(false);
        }

        if (toCur === fromCur) return setConvertedAmt(amount);

        convert();
      } catch (err) {
        console.log("Caught error: " + err.message);
        setError(err.message);
      } finally {
        setIsLoading(false);
      }
    },
    [amount, fromCur, toCur]
    // API gets error if to/from are the same, don't convert
  );
```
