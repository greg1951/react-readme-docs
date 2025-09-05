
# Document Focus

* Rendering different content based on Props
* Building Wrapper components
* Building Polymorphic components
* Forwarding Refs 
* Exposing Component APIs
 
**TABLE OF CONTENTS** 
1. [Document Focus](#document-focus)
2. [Discriminated Unions](#discriminated-unions)
# Discriminated Unions

The ```InfoBox``` component referenced in the exercise really required different input props. The *Hint* InfoBox does not need the *severity* prop argument. Making severity optional was not a good solution since **it is required** by the warning InfoBox.

The best solution was to create separate prop types for hint and warning and then to define a new type that references a union for each.

```javascript
import {type ReactNode} from 'react';
type HintBoxProps = {
    mode: 'hint';
    children: ReactNode;
}
type WarningBoxProps = {
    mode: 'warning';
    severity?: 'low' | 'medium' | 'high';
    children: ReactNode;
}
type InfoBoxProps = HintBoxProps | WarningBoxProps;

}
export default function InfoBox({mode, children}: InfoBoxProps) {
    if (mode === 'hint') {
        return (
            <aside className='infobox infobox-hint'>
                {children}
            </aside>
    );
    }
    //else warning
    const {severity} = props;
    return (
        <aside className={`infobox infobox-warning warning--${severity}`}>
            <h2>Warning</h2>
            <p>{children}</p>
        </aside>
    );
}
```




