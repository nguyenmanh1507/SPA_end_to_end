# Chap 2 translate

## Reintroducing JavaScript

---

### 2.3 Advanced variable hoisting and the execution context object

#### 2.3.1 Hoisting

JavaScript engine tạo ra 2 pass trên code khi nó đi vào scope. Pass đầu tiên nó khởi tạo các biến và pass thứ hai nó thực thi code.

Pass đầu tiên, JavaScript engine đi qua code và làm 3 điều:

1. Khai báo và khởi tạo function arguments.
2. Khai báo các biến cục bộ, bào gồm cả các anonymous function được gán vào biến cục bộ, nhưng không khởi tạo chúng.
3. Khai báo và khởi tạo các function.

```javascript
function myFunction( arg1, arg2 ) { // - (1)
  var local_var = 'foo',            // --- 
      a_function = function() {     // (2)
        console.log('a function');  //
      };                            // --

  function inner() {                // ---
    console.log('inner');           // (3)
  }                                 // ---
}

myFunction(1, 2);
```

Các giá trị **không** được gán vào các biến trong pass đầu tiên bởi vì code có thể phải được thực thi để xác định giá trị mà first pass thì không thực thi code. Các giá trị được gán vào arguments bởi vì bất kỳ code nào cần phải xác định giá trị của argument được chạy trước khi argument được truyền vào trong function.

Example:

```javascript
var regular_joe = 'regular_joe is assigned';
function prison() {
  console.log(regular_joe);
  var regular_joe;
}

prison(); // - output: undefined
          // biến regular_joe đã bị hoist lên top của function
          // và bị kiểm tra trước khi tìm regular_joe ở global scope.
```

```javascript
var regular_joe = 'regular_joe is assigned';
function prison(regular_joe) {
  console.log(regular_joe); // - output: the regular_joe argument. (1)
  var regular_joe;

  console.log(regular_joe); // - output: the regular_joe argument. (2)
}

prison('the regular_joe argument');
// (1): Argument được khai báo value trong first pass.
// Nếu không hiểu 2 pass JavaScript engine,
// nó trông giống như regular_joe argument bị ghi đè
// bởi regular_joe local variable declaration bị hoist.

// (2): Do regular_joe được gán giá trị từ
// argument, nó không bị ghi đè với undefined khi
// nó được khai báo.
```

#### 2.3.2 Execution context and the execution context object

_JavaScript engine lưu trữ các biến như là attributes trên một object được gọi là execution context object._

Mỗi lần function được gọi, có một execution context mới. Execution context là một khái niệm, khái niệm của một running function - nó không phải là object. Nó giống như nghĩ về một vđv trong một running context hoặc một jumping context. Chúng ta có thể nói một vđv đang chạy thay vi một vđv ở trong một running context, giống như nói về một running function, nhưng đó không phải là cách làm việc của thuật ngữ này. Chúng ta gọi là execution context.

Execution context được tạo bởi bất cứ thứ gì xảy ra trong khi function được thực thi. Điều này tách biệt khỏi một function declaration, bởi vì function declaration mô tả cái _sẽ_ xảy ra khi function được thực thi. Execution context _là_ việc thực thi của function.

Tất cả các biến và function được định nghĩa trong một function được coi là một phần của execution context. Execution context là một phần của cái developer đề cập đến khi họ nói chuyện về scope của function. Một biến được coi là "in scope" nếu nó có thể truy cập ở trong execution context hiện tại, đó là cách nói khác của biến trong scope nếu nó có thể truy cập trong khi function đang chạy.

Các biến và function là một phần của execution context được lưu trữ trên execution context object, một implementation của ECMA standard cho execution context. Execution context object là một object trong JavaScript engine, và không phải là biến có thể truy cập trực tiếp trong JavaScript. Nó đủ dễ dàng để truy cập gián tiếp, như mọi lần bạn sử dụng biến bạn đang truy cập vào một attribute của một execution context object.

Trước đó, chúng ta đã nói về cách JavaScript engine tạo 2 pass trên execution context, khai báo và khởi tạo biến, nhưng biến được lưu trữ ở đâu? JavaScript engine khai báo và khởi tạo các biến như là attributes trên execution context object. Ví dụ về cách các biến được lưu trữ:

| Code                                   | Execution context object
|----------------------------------------|--------------------------------
| var ex_variable = 'example',           | {
|     another_example = 'another'        |    ex_variable = 'example',
| ;                                      |    another_example = 'another'
|                                        | }

Hiểu execution context object sẽ là key để hiểu các chương còn lại, vì vậy hãy xem vòng đời của một execution context object và JavaScript code cái mà tạo ra nó.

