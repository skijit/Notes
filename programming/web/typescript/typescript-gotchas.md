TypeScript Gotchas
========================

- `let` vs `const`
    - same scoping rules
    - `const` only means the reference cannot change.
        - **The internal values are still mutable!**
    - To prevent value modification, use `readonly`
- Vscode compliation incompatibility
    - Sometimes Vscode's typescript intellisense/compiler will not observe your tsconfig.json settings.
        - Your build will succeed, but your intellisense will complain
    - Reason: VSCode is using an older version of typescript than your project, so it uses its own build settings
    - Solution: Add the line in your ./vscode/settings.json: `"typescript.tsdk": "node_modules/typescript/lib"`
    - [More info](http://stackoverflow.com/questions/38268776/why-does-typescript-ignore-my-tsconfig-json-inside-visual-studio-code)
- Dictionary Types
    - See [this](https://visualstudiomagazine.com/articles/2016/01/01/exploiting-typescript-arrays.aspx) article
    - Can apply to predefined interfaces or be defined inline
    - The property, or key is in square brackets
    - Example:
        ```(typescript)
        interface IArray {
            [position: number]: Customer;   
        }
        var custs: IArray = [new Customer("A123"), new Customer("B456")];
        ```
        - Client code would look like:
        ```(typescript)
        var cust: Customer
        cust = cust[0];
        ```
    - Another example
    ```(typescript)
    interface IDictionary {
        [key: string]: Customer;    
    };
    ```
    - This was what originally was confusing:
    ```(typescript)
    static productExists(control: Control): {[key: string]: any} { 
        //...
    }
    ```
        - Basically it is saying that it returns an object that has an indexable property (that is a string) and it points to an any object.
        - For example
        ```(javascript)
        {
            fail1: {
                //anything
            },
            fail2: {
                //yadda yadda
            }
        }
        ```