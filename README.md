
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

---------

# Unit testing tips

### Dir structure
We have decided to put unit tests next to tested files.
 
 ##### Cons: 
 1. Creates a bit more noise. Because test files are next to tested files.
 ##### Pros: 
 1. We void directory duplication by replicating the orginal code dir structure in a nested test directory.
 2. We avoid desynchronization between tested directory and original code dir. In practice people tend to update them when refactoring original code directories.
 
 ------
 ### Exports / Imports
 Due to mocking and to create convention it is better to always use similar approaches in the project. 
 * Please prefer default exports.
 * Exporting const or functions is okay although these are a bit more difficult to mock than default exports.
 * Please avoid barrel exports they are more difficult to mock.
 
-----
### General tips

* **Flexibility**: Remember our code coverage goal is around 60%-70%. This gives us a good margin for when a test is too
 difficult or time consuming just skip it due to time constraints and diminishing returns.
* **Don't sweat the small things**: We should focus on testing important parts of the code. Trying to achieve 100% 
test coverage is usually a waste of time for non-critical apps.
* **AAA**: Remember each test should be Arranged, Acted and Asserted.
* **mockFactories**: Use mockFactories to avoid repetition of Entities.
* Remember to check `src/setupTests.ts` it has mocks shared in ALL the unit tests. If something needs to be mocked for 
all tests this is the place to put it. But try to avoid it as much as possible. Usually its better to keep mocks as near as possible to the test.
-----

### Mocking examples for easy referencing:

* Remember mocks usually go at the beginning of the file or at the beginning of the unit test (Arrange in AAA). After mocking you can Act and then Assert.
* As much as it is possible keep your mocks inside your unit test: Keep your enemies near, and also keep your mocks near. This allows easier debugging.
 
#### 1. Mocking an external dependency/library:
In the following mock, we mock `react-router-dom` library. We use jest.requireActual() to bring all other methods and
 just override with custom code what we need in this case the `<Link>` component 

    jest.mock('react-router-dom', () => ({
      ...jest.requireActual('react-router-dom'),
      Link: jest.fn().mockImplementation(({ children }) => {
        return children;
      }),
    }));
    
    describe('<MyComponent>',()=>{...})   // **NOTICE** the mock should go before the test suite

###### Reasons to do this: 
* You are testing a component which uses this external library and it is throwing some errors.
* The external dependency does API calls which you want to avoid.
    
#### 2. Mocking a default exported component
Using default exports usually are the easiest to mock with jest.

    jest.mock('components/LoadMore', () => ({
      __esModule: true,
      default: () => <div>MOCK_LOAD_MORE</div>,
    }));
    
    describe('<MyComponent>',()=>{...})   // **NOTICE** the mock should go before the test suite

###### Reasons to do this: 
* You are testing a parent component which uses the child component (in the above case <LoadMore> component). 
* You want to avoid testing the child component in the parent component.
* You want to avoid the http calls or other async methods of the child component which can cause problems 
when testing the parent component.

#### 3. Mocking inside the test 
      
      test('Some description here', () => {
        // internal mock of sync function
        jest.spyOn(userStorage, 'getUser').mockReturnValueOnce(mockUser());
          
        // internal mock of async function
        jest.spyOn(apiBreeder, 'getBreeder').mockImplementationOnce(async () => {
          throw new Error(getBreederErrorTxt);
        });
           
        // then you can test
      })
      
    
#### 4. Mocking an async http call

    jest.mock('utilities/http/api/apiBreeder', () => ({
      getBreeder: jest.fn(function async() {    //**NOTICE** the async
        return {
          json: { user: { breeders: [mockBreeder()] } },
          status: 200,
        };
      }),
    }));

###### Reasons to do this: 
* You want to avoid the http call in the unit tests, otherwise it will throw false positives/errors.
* You want to test a success or failure response then you mock whatever return value you need.
-----



