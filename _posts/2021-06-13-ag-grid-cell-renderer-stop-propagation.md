---
layout: post
title: How to stop propagation in React AG-Grid custom cell renderers
subtitle: The right way to stop propagation in React AG-Grid custom cell renderers
tags: [react, ag-grid]
readtime: true
---

In AG-Grid you might need to have a button in a specific cell that does something for you.  
In that case you have to implement your custom cell renderer.

My use case is I have enabled the row selection feature and I need to have a button inside a cell.

So every time I click on the button the row is selected/deselected based on the current state of the row selection status.

This was an issue to me, I needed to prevent that action.

The usual way to do this is use `event.preventDefault()` and `event.stopPropagation()`, when I tried to do this it doesn't work.

Take a look at what I have done to make it work.

_This tutorial is assuming you know how to use **react** and integrate **AG-Grid** with it._  
_It is inspired from the official documentation of AG-Grid [Cell Renderer][cellRenderer] and this [StackOverFlow answer][stackOverFlow]._

The official documentation shows you how to create a custom cell renderer like this:
```javascript
// totalValueRenderer.jsx
import React from 'react';

export default (props) => {
  const cellValue = props.valueFormatted ? props.valueFormatted : props.value;

  const buttonClicked = () => {
    alert(`${cellValue} medals won!`);
  };

  return (
    <span>
      <span>{cellValue}</span>&nbsp;
      <button onClick={() => buttonClicked()}>Push For Total</button>
    </span>
  );
};
```

and here how to use it in your data grid using `frameworkComponents`.  
[Read more about it][frameworkComponents]
```javascript
// in index.jsx
...
frameworkComponents={
  {
    totalValueRenderer: TotalValueRenderer,
  }
}
...
```

I have enabled the row selection feature in the `<AgGridReact>` tag attributes
```javascript
...
rowSelection={ 'multiple' }
rowMultiSelectWithClick={ true }
...
```

As suggested in the [StackOverFlow][stackOverFlow] answer is to use the DOM native event instead of react's synthetic event.
```javascript
import React from 'react';

export default (props) => {
  const cellValue = props.valueFormatted ? props.valueFormatted : props.value;

  const buttonClicked = (event) => {
    alert(`${cellValue} medals won!`);
  };

  return (
    <span>
      <span>{cellValue}</span>&nbsp;
      <button
        ref={(ref) => {
          if (!ref) return;
          ref.onclick = (event) => {
            console.log('native event handler');
            // this now will work and 
            // stops the row selection when you click the button inside the cell
            event.stopPropagation();
            
            // put your logic here instead because e.stopPropagation() will
            // stop React's synthetic event
            buttonClicked();
          };
        }}
        // onClick={(event) => {
        //     event.stopPropagation(); // this is not working
        //     buttonClicked()
        //   }
        // }
      >
        Push For Total
      </button>
    </span>
  );
};

```

[cellRenderer]: https://www.ag-grid.com/react-grid/component-cell-renderer/
[stackOverFlow]: https://stackoverflow.com/a/63968681/5423233
[frameworkComponents]: https://www.ag-grid.com/react-grid/components/#registering-custom-components-2