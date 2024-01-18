# Clickable

Sometimes we may want to allow users to click on container controls that contain multiple other controls, but are not clickable out-of-the-box


https://github.com/stadium-software/clickable/assets/2085324/2dccd128-f330-4ec0-be13-96c870bee0b1


## Description
The script below allows you to make any container control clickable. When clicked, it passes the clicked control to a callback script. In that callback script you can use a Javascript action to interrogate the container and extract any other contro and content it contains 

## Version
1.0 initial release

# Setup

## Application Setup
1. Check the Enable Style Sheet checkbox in the application properties

## Global Script Setup
1. Create a Global Script called "Clickable"
2. Add the following input parameters to the Global Script
   1. EventHandler
   2. RepeaterClass
3. Drag a JavaScript action into the script
4. Add the Javascript below into the JavaScript code property
```javascript
/* Stadium Script V1.0 */
let scope = this;
let eventHandlerScript = ~.Parameters.Input.EventHandler;
let parentClass = ~.Parameters.Input.RepeaterClass;
let parent = document.body;
if (parentClass) parent = document.querySelector("." + parentClass);
let clicked = (e) => {
    observer.disconnect();
    if (e.target.classList.contains("stadium-clickable")) {
        scope[eventHandlerScript](e.target);
    } else { 
        scope[eventHandlerScript](e.target.closest(".stadium-clickable"));
    }
    observer.observe(parent, options);
 };
let initClickable = () => {
    if (parent) {
        observer.disconnect();
        let clickable = parent.querySelectorAll(".stadium-clickable");
        for (let i = 0; i < clickable.length; i++) {
            clickable[i].removeEventListener("click", clicked);
            clickable[i].addEventListener("click", clicked, { once: true });
            clickable[i].style.cursor = "pointer";
        }
        observer.observe(parent, options);
    }
 };
let options = {
        childList: true,
    },
    observer = new MutationObserver(initClickable);
initClickable();
```

## Page Setup
1. Drag a container control to the page (e.g. a *Container*)
2. Add a class called "stadium-clickable" to the control
3. Drag other controls into the *Container* control

## Event Handler Setup
1. Create a script under the page (call it anything you like e.g. "ClickEventHandler")
2. Add an input parameter to the script and call it "Control"

![](images/event-handler-input.png)

3. Use a Javascript action to extract content from the input parameter
4. Example:
```javascript
let el = ~.Parameters.Input.Control;
return el.querySelector(".label-container").textContent;
```

![](images/click-event-handler.png)

## Page.Load Setup
1. Drag the Global Script called "Clickable" into the Page.Load event handler
2. Provide values for the script parameters
   1. EventHandler: The name of the page-level script where you will handle the click event (e.g. "ClickEventHandler")
   2. RepeaterClass (only necessary when the clickable container control is inside a repeater control): Assign a unique classname to the repeater control and supply it as the parameter here

![](images/script-parameters.png)