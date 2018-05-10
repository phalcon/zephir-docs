Classes and Objects
===================
Zephir promotes object-oriented programming. This is why you can only export methods and classes in extensions. Also you will
see that, most of the time, runtime errors raise exceptions instead of fatal errors or warnings.

Classes
-------
Every Zephir file must implement a class or an interface (and just one). A class structure is very similar to a PHP class:

.. code-block:: zephir

    namespace Test;

    /**
     * This is a sample class
     */
    class MyClass
    {

    }

Class Modifiers
^^^^^^^^^^^^^^^
The following class modifiers are supported:

*Final*: If a class has this modifier it cannot be extended:

.. code-block:: zephir

    namespace Test;

    /**
     * This class cannot be extended by another class
     */
    final class MyClass
    {

    }

*Abstract*: If a class has this modifier it cannot be instantiated:

.. code-block:: zephir

    namespace Test;

    /**
     * This class cannot be instantiated
     */
    abstract class MyClass
    {

    }

Implementing Methods
--------------------
The "function" keyword introduces a method. Methods implement the usual visibility modifiers available in PHP. Explicity
setting a visibility modifier is mandatory in Zephir:

.. code-block:: zephir

    namespace Test;

    class MyClass
    {

        public function myPublicMethod()
        {
            // ...
        }

        protected function myProtectedMethod()
        {
            // ...
        }

        private function myPrivateMethod()
        {
            // ...
        }
    }

Methods can receive required and optional parameters:

.. code-block:: zephir

    namespace Test;

    class MyClass
    {

        /**
         * All parameters are required
         */
        public function doSum1(a, b)
        {
            return a + b;
        }

        /**
         * Only 'a' is required, 'b' is optional and it has a default value
         */
        public function doSum2(a, b = 3)
        {
            return a + b;
        }

        /**
         * Both parameters are optional
         */
        public function doSum3(a = 1, b = 2)
        {
            return a + b;
        }

        /**
         * Parameters are required and their values must be integer
         */
        public function doSum4(int a, int b)
        {
            return a + b;
        }

        /**
         * Static typed with default values
         */
        public function doSum4(int a = 4, int b = 2)
        {
            return a + b;
        }
    }

Optional nullable parameters
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Zephir ensures that the value of a variable remains of the type the variable was declared as. This makes Zephir convert the
null value to the closest approximate value:

.. code-block:: zephir

    public function foo(int a = null)
    {
        echo a; // if "a" is not passed it prints 0
    }

    public function foo(boolean a = null)
    {
        echo a; // if "a" is not passed it prints false
    }

    public function foo(string a = null)
    {
        echo a; // if "a" is not passed it prints an empty string
    }

    public function foo(array a = null)
    {
        var_dump(a); // if "a" is not passed it prints an empty array
    }

Supported Visibilities
^^^^^^^^^^^^^^^^^^^^^^
* Public: Methods marked as "public" are exported to the PHP extension; this means that public methods are visible to the
PHP code as well to the extension itself.

* Protected: Methods marked as "protected" are exported to the PHP extension; this means that protected methods are visible
to the PHP code as well to the extension itself. However, protected methods can only be called in the scope of the class or
in classes that inherit them.

* Private: Methods marked as "private" are not exported to the PHP extension; this means that private methods are only
visible to the class where they're implemented.

Supported Modifiers
^^^^^^^^^^^^^^^^^^^
* Static: Methods with this modifier can only be called in a static context (from the class, not an object).

* Final: If a method has this modifier it cannot be overriden.

* Deprecated: Methods marked as "deprecated" throw an E_DEPRECATED error when they are called.

Getter/Setter shortcuts
^^^^^^^^^^^^^^^^^^^^^^^
Like in C#, you can use get/set/toString shortcuts in Zephir. This feature allows you to easily write setters and getters for
properties, without explictly implementing those methods as such.

For example, without shortcuts we would need code like:

.. code-block:: zephir

    namespace Test;

    class MyClass
    {
        protected myProperty;

        protected someProperty = 10;

        public function setMyProperty(myProperty)
        {
            let this->myProperty = myProperty;
        }

        public function getMyProperty()
        {
            return this->myProperty;
        }

        public function setSomeProperty(someProperty)
        {
            let this->someProperty = someProperty;
        }

        public function getSomeProperty()
        {
            return this->someProperty;
        }

        public function __toString()
        {
            return this->myProperty;
        }

     }

You can write the same code using shortcuts as follows:

.. code-block:: zephir

    namespace App;

    class MyClass
    {
        protected myProperty {
            set, get, toString
        };

        protected someProperty = 10 {
            set, get
        };

    }

When the code is compiled, those methods are exported as real methods, but you don’t have to write them manually.

Return Type Hints
^^^^^^^^^^^^^^^^^
Methods in classes and interfaces can have "return type hints". These will provide useful extra information to the compiler
to inform you about errors in your application. Consider the following example:

.. code-block:: zephir

    namespace App;

    class MyClass
    {
        public function getSomeData() -> string
        {
            // this will throw a compiler exception
            // since the returned value (boolean) does not match
            // the expected returned type string
            return false;
        }

        public function getSomeOther() -> <App\MyInterface>
        {
            // this will throw a compiler exception
            // if the returned object does not implement
            // the expected interface App\MyInterface
            return new App\MyObject;
        }

        public function process()
        {
            var myObject;

            // the type-hint will tell the compiler that
            // myObject is an instance of a class
            // that implement App\MyInterface
            let myObject = this->getSomeOther();

            // the compiler will check if App\MyInterface
            // implements a method called "someMethod"
            echo myObject->someMethod();
        }

    }

A method can have more than one return type. When multiple types are defined, the operator | must be used to separate those
types.

.. code-block:: zephir

    namespace App;

    class MyClass
    {
        public function getSomeData(a) -> string | bool
        {
            if a == false {
                return false;
            }
            return "error";
        }
    }

Return Type: Void
^^^^^^^^^^^^^^^^^
Methods can also be marked as ‘void’. This means that a method is not allowed to return any data:

.. code-block:: zephir

    public function setConnection(connection) -> void
    {
        let this->_connection = connection;
    }

Why is this useful? Because the compiler can detect if the program is expecting a return value from these methods, and
produce a compiler exception:

.. code-block:: zephir

    let myDb = db->setConnection(connection); // this will produce an exception
    myDb->execute("SELECT * FROM robots");

Strict/Flexible Parameter Data-Types
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
In Zephir, you can specify the data type of each parameter of a method. By default, these data-types are flexible; this means
that if a value with a wrong (but compatible) data-type is passed, Zephir will try to transparently convert it to the
expected one:

.. code-block:: zephir

    public function filterText(string text, boolean escape=false)
    {
        //...
    }

Above method will work with the following calls:

.. code-block:: php

    <?php

    $o->filterText(1111, 1); // OK
    $o->filterText("some text", null); // OK
    $o->filterText(null, true); // OK
    $o->filterText("some text", true); // OK
    $o->filterText(array(1, 2, 3), true); // FAIL

However, passing a wrong type could often lead to bugs. Improper use of a specific API would produce unexpected results. You
can disallow the automatic conversion by setting the parameter with a strict data-type:

.. code-block:: zephir

    public function filterText(string! text, boolean escape=false)
    {
        //...
    }

Now, most of the calls with a wrong type will cause an exception due to the invalid data types passed:

.. code-block:: php

    <?php

    $o->filterText(1111, 1); // FAIL
    $o->filterText("some text", null); // OK
    $o->filterText(null, true); // FAIL
    $o->filterText("some text", true); // OK
    $o->filterText(array(1, 2, 3), true); // FAIL

By specifying what parameters are strict and what can be flexible, a developer can set the specific behavior he/she really
wants.

Read-Only Parameters
^^^^^^^^^^^^^^^^^^^^
Using the keyword 'const' you can mark parameters as read-only, this helps to respect `const-correctness
<http://en.wikipedia.org/wiki/Const-correctness>`_. Parameters marked with this attribute cannot be modified inside the
method:

.. code-block:: zephir

    namespace App;

    class MyClass
    {
        // "a" is read-only
        public function getSomeData(const string a)
        {
            // this will throw a compiler exception
            let a = "hello";
        }
    }

When a parameter is declared as read-only, the compiler can make safe assumptions and perform further optimizations over
these variables.

Implementing Properties
-----------------------
Class member variables are called "properties". By default, they act the same as PHP properties. Properties are exported to
the PHP extension, and are visible from PHP code. Properties implement the usual visibility modifiers available in PHP, and
explicity setting a visibility modifier is mandatory in Zephir:

.. code-block:: zephir

    namespace Test;

    class MyClass
    {

        public myProperty1;

        protected myProperty2;

        private myProperty3;

    }

Within class methods, non-static properties may be accessed by using -> (Object Operator):

.. code-block:: zephir

    namespace Test;

    class MyClass
    {

        protected myProperty;

        public function setMyProperty(var myProperty)
        {
            let this->myProperty = myProperty;
        }

        public function getMyProperty()
        {
            return this->myProperty;
        }
    }

Properties can have literal compatible default values. These values must be able to be evaluated at compile time and must not
depend on run-time information in order to be evaluated:

.. code-block:: zephir

    namespace Test;

    class MyClass
    {

        protected myProperty1 = null;
        protected myProperty2 = false;
        protected myProperty3 = 2.0;
        protected myProperty4 = 5;
        protected myProperty5 = "my value";
    }

Updating Properties
^^^^^^^^^^^^^^^^^^^
Properties can be updated by accesing them using the '->' operator:

.. code-block:: zephir

    let this->myProperty = 100;

Zephir checks that properties exist when a program is accesing them. If a property is not declared, you will get a compiler
exception:

.. code-block:: php

    CompilerException: Property '_optionsx' is not defined on class 'App\MyClass' in /Users/scott/utils/app/myclass.zep on line 62

          let this->_optionsx = options;
          ------------^

If you want to avoid this compiler validation, or just create a property dynamically, you can enclose the property name using
brackets and string quotes:

.. code-block:: zephir

    let this->{"myProperty"} = 100;

You can also use a simple variable to update a property; the property name will be taken from the variable:

.. code-block:: zephir

    let someProperty = "myProperty";
    let this->{someProperty} = 100;

Reading Properties
^^^^^^^^^^^^^^^^^^
Properties can be read by accesing them using the '->' operator:

.. code-block:: zephir

    echo this->myProperty;

As when updating, properties can be dynamically read this way:

.. code-block:: zephir

    //Avoid compiler check or read a dynamic user defined property
    echo this->{"myProperty"};

    //Read using a variable name
    let someProperty = "myProperty";
    echo this->{someProperty}

Class Constants
---------------
Classes may contain class constants that remain the same and unchangeable once the extension is compiled. Class constants are
exported to the PHP extension, allowing them to be used from PHP.

.. code-block:: zephir

    namespace Test;

    class MyClass
    {

        const MYCONSTANT1 = false;
        const MYCONSTANT2 = 1.0;
    }

Class constants can be accessed using the class name and the static operator (::):

.. code-block:: zephir

    namespace Test;

    class MyClass
    {

        const MYCONSTANT1 = false;
        const MYCONSTANT2 = 1.0;

        public function someMethod()
        {
            return MyClass::MYCONSTANT1;
        }
    }

Calling Methods
---------------
Methods can be called using the object operator (->) as in PHP:

.. code-block:: zephir

    namespace Test;

    class MyClass
    {

        protected function _someHiddenMethod(a, b)
        {
            return a - b;
        }

        public function someMethod(c, d)
        {
            return this->_someHiddenMethod(c, d);
        }
    }

Static methods must be called using the static operator (::):

.. code-block:: zephir

    namespace Test;

    class MyClass
    {

        protected static function _someHiddenMethod(a, b)
        {
            return a - b;
        }

        public static function someMethod(c, d)
        {
            return self::_someHiddenMethod(c, d);
        }
    }

You can call methods in a dynamic manner as follows:

.. code-block:: zephir

    namespace Test;

    class MyClass
    {
        protected adapter;

        public function setAdapter(var adapter)
        {
            let this->adapter = adapter;
        }

        public function someMethod(var methodName)
        {
            return this->adapter->{methodName}();
        }
    }

Parameters by Name
^^^^^^^^^^^^^^^^^^
Zephir supports calling method parameters by name or keyword arguments. Named parameters can be useful if you want to pass
parameters in an arbitrary order, document the meaning of parameters, or specify parameters in a more elegant way.

Consider the following example. A class called “Image” has a method that receives four parameters:

.. code-block:: zephir

    namespace Test;

    class Image
    {
        public function chop(width = 600, height = 400, x = 0, y = 0)
        {
            //...
        }
    }

Using the standard method calling approach:

.. code-block:: zephir

    i->chop(100); // width=100, height=400, x=0, y=0
    i->chop(100, 50, 10, 20); // width=100, height=50, x=10, y=20

Using named parameters, you can:

.. code-block:: zephir

    i->chop(width: 100); // width=100, height=400, x=0, y=0
    i->chop(height: 200); // width=600, height=200, x=0, y=0
    i->chop(height: 200, width: 100); // width=100, height=200, x=0, y=0
    i->chop(x: 20, y: 30); // width=600, height=400, x=20, y=30

When the compiler (at compile time) does not know the correct order of these parameters, they must be resolved at runtime. In
this case, there could be a minimum additional extra overhead:

.. code-block:: zephir

    let i = new {someClass}();
    i->chop(y: 30, x: 20);
