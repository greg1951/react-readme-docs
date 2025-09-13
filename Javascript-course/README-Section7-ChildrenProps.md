1. [Reusable Buttons Example](#reusable-buttons-example)
   1. [Button Component Before Children Props](#button-component-before-children-props)
   2. [Button Component After Children Props](#button-component-after-children-props)
# Reusable Buttons Example

The V1 version of the Steps app (a VSCode project) has two buttons to drive *Previous* and *Next* functionality. In this example, the buttons are reimplemented using a **reusable Button component** and **children properties**. 

**The props.children feature is a useful technique to inject element content into a component, making a component more generic and reusable.**

In the screenshot below, the emojis were placed in a fixed position before the text. The change utilizing children properties would place the emjis before and after the text, depeding on whether it is Previous or Next. The Button compoment in this case uses the reserved **React children prop** to do this.

![Steps UI Before Change](readme-screenshots/sections1-7/steps-b4-children.png)

## Button Component Before Children Props
The desired button look and feel is to make the Button component flexible such that we can avoid having fixed positions in the rendering of the emoji in previous and next buttons. This is where JSX can be wrapped around the emoji and text to allow props children to be used to so the **Previous button** rendering is used more flexibly.

![Steps UI After Change](readme-screenshots/sections1-7/steps-after-children.png)

```javascript
  ...
  return (
    <div className="steps">
      <div className="numbers">
        <div className={`${step >= 1 ? "active" : ""}`}>1</div>
        <div className={`${step >= 2 ? "active" : ""}`}>2</div>
        <div className={`${step >= 3 ? "active" : ""}`}>3</div>
      </div>

      <p className="message">Step {step}: {messages[step - 1]}</p>

      <div className="buttons">
        <Button bgColor="#7950f2" textColor="#fff" onClick={handlePrevious} emoji="👈" text="Previous" />
        <Button bgColor="#7950f2" textColor="#fff" onClick={handleNext} emoji="👉" text="Next" />
      </div>

    </div>
  );
  function Button({bgColor, textColor, onClick, text, emoji}) {
    return (
      <button style={
        {backgroundColor: bgColor, color: textColor}} 
        onClick={onClick}>
          <span>{emoji}{text}</span></button>
    );
  }
```

## Button Component After Children Props
The **Button component** was changed to *remove the emoji and text props*, as this is now part of the chidren props passed in the buttons div elements. Rather than closing up the Button component, write the  **span element** outside of it and then close the Button component, as shown below.

**Without Children Prop**: ```<Button props... />```

**With Children Prop**: ```<Button props>...>JSX children content </Button>```


Note **Button component** references the **children** prop, at the place where the content is to be rendered. This makes the Button component easier to reuse-as it doesn't need to know about the content.

```javascript
  return (
    <div className="steps">
      <div className="numbers">
        <div className={`${step >= 1 ? "active" : ""}`}>1</div>
        <div className={`${step >= 2 ? "active" : ""}`}>2</div>
        <div className={`${step >= 3 ? "active" : ""}`}>3</div>
      </div>

      <p className="message">Step {step}: {messages[step - 1]}</p>

      <div className="buttons">
        <Button bgColor="#7950f2" textColor="#fff" onClick={handlePrevious}>
          <span>👈Previous</span> 
        </Button>
        <Button bgColor="#7950f2" textColor="#fff" onClick={handleNext}>
          <span>Next👉</span> 
        </Button>
      </div>

    </div>
  );
  function Button({bgColor, textColor, onClick, children}) {
    return (
      <button style={
        {backgroundColor: bgColor, color: textColor}} 
        onClick={onClick}>
          {children}    
      </button>
    );
  }
```

