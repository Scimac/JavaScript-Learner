## Events

##### Difference between addEventListener and onclick in JavaScript
- <table><tbody><tr><td><p style="text-align:center"><strong>addEventListener</strong></p></td><td><p style="text-align:center"><strong>onclick</strong></p></td></tr><tr><td>addEventListener can add multiple events to a particular element.</td><td>onclick can add only a single event to an element. It is basically a property, so gets overwritten.</td></tr><tr><td>addEventListener can take a third argument that can control the event propagation.</td><td>Event propagation cannot be controlled by onclick.</td></tr><tr><td>addEventListener can only be added within &lt;script&gt; elements or in external JavaScript file.</td><td>onclick can be added as an HTML attribute also.</td></tr><tr><td>addEventListener does not work in older versions of Internet explorer, which uses attachEvent instead.</td><td>onclick works in all browsers.</td></tr></tbody></table>
- `element.addEventListener(event, listener, useCapture);`
- **Parameters:**
	-   **event:** Event can be any valid JavaScript event. Events are used without the “on” prefix like use “click” instead of “onclick” or “mousedown” instead of “onmousedown”.
	-   **listener(handler function):** It can be a JavaScript function that responds to the event that occurs.
	-   **useCapture:**  (Optional parameter) A Boolean value that specifies whether the event should be executed in the capturing or in the bubbling phase.

##### Heading

