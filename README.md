[js-runtime-typechecking](https://dirkarnez.github.io/js-runtime-typechecking)
==============================================================================
### Application
- very basic typechecking extension development for browser environment where compile-time typechecking (e.g. TypeScript) is not possible

### Notes
- Full runtime typechecking is almost impossible. It is because the untyped objects / variables may have different underlying type at any time. Guess these underlying types requires complete parsing and evaluting (e.g. do  `fetch`) the code, this mean full runtime type-check cannot be done before running the code, but after running the code, which means we can only do type assertion. The following POC used acorn.js demonstrates how unrealistic is it to do runtime typechecking (We can track the `"ReturnStatement"`s but we still have no idea what underlying types is these returns return. 
 - ```js
    const myClassAST = parse(`class Rectangle {
        constructor(height, width) {
            this.height = height;
            this.width = width;
        }

        draw(graph) {
            return this.width + this.height;
            graph.draw(this.width, this.height);
        }
        log() {
           console.log("drawing rectangle");
        }
    }`, { ecmaVersion: 2020 });
    const myClassASTAsJson = JSON.stringify(myClassAST);
    let clazz = undefined;
    let clazzBodyBody = []; // contains methods and contructor(s)
    
    const typeCheck = 
        Array.isArray(myClassAST.body) && 
        myClassAST.body.length == 1 && 
        (clazz  = myClassAST.body[0]).type == "ClassDeclaration" && 
        clazz.type.superClass == null &&
        clazz.body.type == "ClassBody" &&
        Array.isArray((clazzBodyBody = clazz.body.body)) && clazzBodyBody.length == 3;
        // clazzBodyBody[1].key.name = "draw"
        // clazzBodyBody[1].value.params.length
        // "ReturnStatement"
        debugger;
    
    console.log(!!typeCheck ? "ok" : "no ok");
    ```
