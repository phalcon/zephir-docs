Exceptions
==========
Zephir implements exceptions at a very low level, providing similar behavior and functionality to PHP.

When an exception is thrown, a 'catch' block can be used to capture the exception and allow the developer to provide proper
handling:

.. code-block:: zephir

    var e;
    try {

        // exceptions can be thrown here
        throw new \Exception("This is an exception");

    } catch \Exception, e {

        // handle exception
        echo e->getMessage();
    }

Zephir also provides a "silent" 'try' block, that simply ignores any exceptions produced within that block:

.. code-block:: zephir

    try {
        throw new \Exception("This is an exception");
    }

If you don't need an exception variable when 'catch'ing, then you can safely not provide it:

.. code-block:: zephir

    try {

        // exceptions can be thrown here
        throw new \Exception("This is an exception");

    } catch \Exception {

        // handle exception
        echo "An exception occur!";
    }


A single 'catch' block can be used to catch multiple types of exception:

.. code-block:: zephir

    var e;
    try {

        // exceptions can be thrown here
        throw new \Exception("This is an exception");

    } catch \RuntimeException|\Exception, e {

        // handle exception
        echo e->getMessage();
    }

Zephir allows you to throw literals or static typed variables as if they were the message of the exception:

.. code-block:: zephir

    throw "Test"; // throw new \Exception("Test");
    throw 't'; // throw new \Exception((string) 't');
    throw 123; // throw new \Exception((string) 123);
    throw 123.123; // throw new \Exception((string) 123.123);

Zephir's exceptions provide the same methods to know where the exception happened that PHP's exceptions do. That is,
Exception::getFile() and Exception::getLine() return the location in the Zephir code where the exception was thrown:

.. code-block:: html

    Exception: The static method 'someMethod' doesn't exist on model 'Robots'
    File=phalcon/mvc/model.zep Line=4042
    #0 /home/scott/test.php(64): Phalcon\Mvc\Model::__callStatic('someMethod', Array)
    #1 /home/scott/test.php(64): Robots::someMethod()
    #2 {main}
