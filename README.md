
# Unit testing with React hooks

We are using the following dependencies that help us unit test react when using hooks:

    "@testing-library/jest-dom"
    "@testing-library/react"
    "@testing-library/react-hooks"

For documentation and easy to follow examples check:

* https://fullstackopen.com/en/part5/testing_react_apps
* https://www.freecodecamp.org/news/testing-react-hooks/ 

Useful hints:

 * Important use cleanup to avoid memory leaks  documentation here:

 ```
	    import { cleanup, render } from '@testing-library/react'
	    // ...
	    afterEach(cleanup);
```

* use `myComponent.debug()` to print into console your React Component.

* mock child components & dependencies.

* If the test is testing an async function the act() function must be awaited:
```
	it("on click call RecaptchaService.getRecaptchaToken", async () => {
	  // ...
	  await act(async () => {
	    fireEvent.click(getRecaptchaButton);
	  });
	  // ...
	});
```
* To update input value triggering change event (https://testing-library.com/docs/example-input-event): 

      fireEvent.change(input, { target: { value: 'Good Day' } }) 


