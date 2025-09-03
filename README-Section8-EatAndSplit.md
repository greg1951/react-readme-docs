# Section 8 - Eat and Split App
There are no new concepts introduced in this exercise. Its purpose was to reinforce all of the React concepts covered so far in the Udemy course. 

The project resides on a private GitHub repo: [follow this URL](https://githubbox.com/greg1951/06-eat-n-split) to open a browser and preview the project in **CodeSandbox**.

Below is a **static rendering** of the components in the app.

![Split Bill-Static](readme-screenshots/section%208/split-bill-static-rendering.png)

There are five visible components shown above, as outlined in various colors.
1. **List of Friends**
2. **Friend**
3. **Add a Friend** form
4. **Add Friend** button
5. **Split a Bill** form

In addition other utility components (e.g. **Button**) can be derived from the design.

When the component tree design is actualized then a static rendering of the application is begun.

## FriendsList and Friend Components
* Note below the use of the **&& conditional** to style and set the proper wording.

```javascript
function FriendsList() {
  const friends = initialFriends;
  return (
    <div>
      <ul>
        {friends.map(friend=>
          <Friend friend={friend} key={friend.id} />
      )}
      </ul>
    </div>
  );
}

function Friend({friend}) {
  return (
    <li>
      <img src={friend.image} alt={friend.name} />
      <h2>{friend.name}</h2>
      {friend.balance < 0 && <p className="red">You owe {friend.name} ${Math.abs(friend.balance)}</p>}
      {friend.balance > 0 && <p className="green">{friend.name} owes you ${friend.balance}</p>}
      {friend.balance === 0 && <p>You and {friend.name} are even</p>}
      <Button>Select</Button>
    </li>
  );
}
```

The button functionality was componentized, which reused some of the detail about a button (e.g. *className*). It made use of **chidren properties** to render the button name.

```javascript
function Button({children}) {
  return(
    <button className="button">{children}</button>
  )  
}
```
## FormAddFriend Component

The **FormAddFriend** component is rendered in a side bar on the page, providing the list of friends. (Static version is shown below.)

```javascript
function FormAddFriend() {
  return (
    <div>
      <form className="form-add-friend">
        <label>🧑‍🤝‍🧑Friend Name</label>
        <input type="text" placeholder="John..."></input>
        <label>🖼️Image URL</label>
        <input type="text" placeholder="http://..."></input>
        <Button>Add</Button>
      </form>
    </div>
  );
}
```

## FormSplitBill Component

The **FormSplitBill** component was very much like the one above. (The static version is shown below. When a friend is selected, then the friend's name will show up in the form in lieu of *Someone*. )

At some point, controlled elements (state) will be added to capture the values of each input.

```javascript
function FormSplitBill() {
  return (
    <div>
        <form className="form-split-bill">
          <h2>Split Bill with Someone</h2>
          <label>💰Bill Amount</label>
          <input type="text"></input>
          <label>💲Your Expense</label>
          <input type="text"></input>
          <label>🪙Someone's Expense</label>
          <input type="text"></input>
          <label>🤔Who is paying the bill?</label>
          <select>
            <option>You</option>
            <option>Someone</option>
          </select>
          <Button>Split</Button>
          </form>
    </div>
  );
}
```

## Implementing State in Add Friend Component

How to open the form to add a friend is done in the App component where the sidebar is implemented. To know whether the form is open or not qill require state and modification to the Button component to accept an onClick event handler, as shown below.

```javascript
function Button({children, onClick}) {
  return(
    <button className="button" onClick={onClick}>{children}</button>
  )  
}

export default function App() {
  const [showAddFriend, setShowAddFriend] = useState(false);

  function handleShowAddFriend() {
    setShowAddFriend((show) => !show);
  }

  return (
    <div className="app">
      <div className="sidebar">
        <FriendsList />
        {showAddFriend && <FormAddFriend />}
        
        <Button onClick={handleShowAddFriend}>
          {!showAddFriend ? "Add Friend":"Close"}
        </Button>
        </div>
        <FormSplitBill />
    </div>
  );
}
```

Additional behavior implemented in the above JSX is to modify the button text from **Add Friend** to **Close**, once the form is opened. 

![Add Friend-Close Button](readme-screenshots/section%208//add-friend-close-button.png)

## Adding a Friend to the Friends Array

One use of controlled elements in React is to implement state in form fields, as in the case of the **FormAddFriend** component shown below.

1. State variables name and image created for the two form fields.
2. **onChange** events were added to each of the form fields to capture the values entered.
3. Form **onSubmit** event references event handler that sets up the newFriend (push into friends array not implemented).

**Note**: Within handleSubmit function the e.preventDefault() will suppress default behavior to refresh the page. This is necessary to keep the friend add form up if multiple friends are to be added.


```javascript
function FormAddFriend() {
  const [name, setName] = useState("");
  const [image, setImage] = useState("https://i.pravatar.cc/48");


  function handleSubmit(e) {
    e.preventDefault();

    if (!name || !image) return;

    const id = crypto.randomUUID();
    const newFriend = {id
                      ,name
                      ,image: `${image}?=${id}`
                      ,balance: 0
                     }

    // add code here to insert newFriend into friends array
    // ...
    // after adding new friend, reset form for another add
    setName("");
    setImage("https://i.pravatar.cc/48");
  }

  return (
    <div>
      <form className="form-add-friend" onSubmit={handleSubmit}>
        <label>🧑‍🤝‍🧑Friend Name</label>
        <input type="text" 
               value={name} 
               onChange={(e)=>setName(e.target.value)} />
        <label>🖼️Image URL</label>
        <input type="text" 
               value={image} 
               onChange={(e)=>setImage(e.target.value)} />
        <Button>Add</Button>
      </form>
    </div>
  );
}
```

## Adding New Friend to FriendsList

The *newFriend* object created in the **FormAddFriend** component needs to be added to the array of friends and then displayed in the sidebar **FriendsList**. 

As the *FormAddFriend* component is run from the App component, **the state needed to add the new friend to the friends array must be lifted to the App component** and passed from there to the **FormAddFriend** component, as summarized in the snippets below.

1. In the *App* component, the **handleAddFriend** event handler was created to insert the **newFriend** into the friends array using the **setFriends method**. After adding the new friend, then the **setShowAddFriend(false)** will close the *FormAddFriend* component.

    **Note**: *By localizing the update in the handleAddFriend function in *App*, it prevents the *FormAddFriend* from having to know about the *friends* state variable.* This is a good example of child to parent component communication.

2. The **onAddFriend** reference was passed to the **FormAddFriend** component which uses the event handler in the App component to insert the friend and refresh the list of friends in the sidebar.


```javascript
export default function App() {
  const [showAddFriend, setShowAddFriend] = useState(false);
  const [friends, setFriends] = useState(initialFriends);
  ...
  function handleAddFriend(newFriend) {
    setFriends((friends)=>[...friends, newFriend]);
    setShowAddFriend(false);
  }
...
function FormAddFriend({onAddFriend}) {
  const [name, setName] = useState("");
  const [image, setImage] = useState("https://i.pravatar.cc/48");

  function handleSubmit(e) {
    e.preventDefault();
    ...
    // Use parent event handler to perform new friend insert
    onAddFriend(newFriend);
    // Clear form for another add
    setName("");
    setImage("https://i.pravatar.cc/48");
  }}
  ...
```

## Selecting a Friend

The desired behavior is that when a **Friend** component is selected then the **FormSplitBill** component should be rendered, as shown below.

![Split Bill-Close Button](readme-screenshots/section%208//split-bill-close-button.png)

To transfer the select **Friend** component details requires that state be transferred from the Friend component, to the **FormSplitBill** component. To do this, information about the selected Friend then needs to be shared from the top parent component (i.e. **App**).

A React concept referred to as **property drill downs** becomes necessary in order to **pass properties down multiple levels** in the component hierarchy. 

```javascript
export default function App() {
  ...
  const [selectedFriend, setSelectedFriend] = useState(null);
  ...

  function handleSelectFriend(friend) {
    setSelectedFriend((selected)=>selected?.id === friend.id? null: friend);
  }

  return (
    <div className="app">
      <div className="sidebar">
        <FriendsList friends={friends} onSelectFriend={handleSelectFriend} selectedFriend={selectedFriend}/>
        {showAddFriend && <FormAddFriend onAddFriend={handleAddFriend}/>}
        
        <Button onClick={handleShowAddFriend}>
          {!showAddFriend ? "Add Friend":"Close"}
        </Button>
        </div>
        <div>
          {selectedFriend && <FormSplitBill selectedFriend={selectedFriend}/>};
        </div>
    </div>
  );
}
```

A Javascript feature called **optional chaining** was used. Use of the "**?**" syntax after the object name prevents the "*Cannot read properties of null*" error when the selectedFriend object is null (default value), as shown in the snippet below.

```javascript
function Friend({friend, onSelectFriend, selectedFriend}) {
  const isAlreadySelected = selectedFriend?.id === friend.id;
  ...
```

