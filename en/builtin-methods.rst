Built-In Methods
================
As mentioned before, Zephir promotes object-oriented programming. Variables related to static types can also be handled as
objects.

Compare these two methods:

.. code-block:: zephir

    public function binaryToHex(string! s) -> string
    {
        var o = "", n; char ch;

        for ch in range(0, strlen(s)) {
            let n = sprintf("%X", ch);
            if strlen(n) < 2 {
                let o .= "0" . n;
            } else {
                let o .= n;
            }
        }
        return o;
    }

And:

.. code-block:: zephir

    public function binaryToHex(string! s) -> string
    {
        var o = "", n; char ch;

        for ch in range(0, s->length()) {
            let n = ch->toHex();
            if n->length() < 2 {
                let o .= "0" . n;
            } else {
                let o .= n;
            }
        }
        return o;
    }

They both have the same functionality, but the second one uses object-oriented programming. Calling methods on static-typed
variables does not have any impact on performance since Zephir internally transforms the code from the object-oriented
version to the procedural version.

String
^^^^^^
The following string built-in methods are available:

+------------------+----------------------+--------------------------------------------------------------------------------+
| OO               | Procedural           | Description                                                                    |
+------------------+----------------------+--------------------------------------------------------------------------------+
| s->length()      | strlen(s)            | Get string length                                                              |
+------------------+----------------------+--------------------------------------------------------------------------------+
| s->trim()        | trim(s)              | Strip whitespace (or other characters) from the beginning and end of a string  |
+------------------+----------------------+--------------------------------------------------------------------------------+
| s->trimleft()    | ltrim(s)             | Strip whitespace (or other characters) from the beginning of a string          |
+------------------+----------------------+--------------------------------------------------------------------------------+
| s->trimright()   | rtrim(s)             | Strip whitespace (or other characters) from the end of a string                |
+------------------+----------------------+--------------------------------------------------------------------------------+
| s->index("foo")  | strpos(s, "foo")     | Find the position of the first occurrence of a substring in a string           |
+------------------+----------------------+--------------------------------------------------------------------------------+
| s->lower()       | strtolower(s)        | Make a string lowercase                                                        |
+------------------+----------------------+--------------------------------------------------------------------------------+
| s->upper()       | strtoupper(s)        | Make a string uppercase                                                        |
+------------------+----------------------+--------------------------------------------------------------------------------+
| s->lowerfirst()  | lcfirst(s)           | Make a string's first character lowercase                                      |
+------------------+----------------------+--------------------------------------------------------------------------------+
| s->upperfirst()  | ucfirst(s)           | Make a string's first character uppercase                                      |
+------------------+----------------------+--------------------------------------------------------------------------------+
| s->format()      | sprintf(s, "%s", x)  | Return a formatted string                                                      |
+------------------+----------------------+--------------------------------------------------------------------------------+
| s->md5()         | md5(s)               | Calculate the md5 hash of a string                                             |
+------------------+----------------------+--------------------------------------------------------------------------------+
| s->sha1()        | sha1(s)              | Calculate the sha1 hash of a string                                            |
+------------------+----------------------+--------------------------------------------------------------------------------+

Array
^^^^^
The following array built-in methods are available:

+------------------+------------------------+--------------------------------------------------------------------------+
| OO               | Procedural             | Description                                                              |
+------------------+------------------------+--------------------------------------------------------------------------+
| a->join(" ")     | join(" ", a)           | Join array elements with a string                                        |
+------------------+------------------------+--------------------------------------------------------------------------+
| a->rev()         | array_reverse(a)       | Return an array with elements in reverse order                           |
+------------------+------------------------+--------------------------------------------------------------------------+
| a->reversed()    | array_reverse(a)       | Return an array with elements in reverse order                           |
+------------------+------------------------+--------------------------------------------------------------------------+
| a->diff()        | array_diff(a)          | Computes the difference of arrays                                        |
+------------------+------------------------+--------------------------------------------------------------------------+
| a->flip()        | array_flip(a)          | Exchanges all keys with their associated values in an array              |
+------------------+------------------------+--------------------------------------------------------------------------+
| a->walk()        | array_walk(a)          | Apply a user supplied function to every member of an array               |
+------------------+------------------------+--------------------------------------------------------------------------+
| a->hasKey()      | array_key_exists(a)    | Checks if the given key or index exists in the array                     |
+------------------+------------------------+--------------------------------------------------------------------------+
| a->keys()        | array_keys(a)          | Return all the keys or a subset of the keys of an array                  |
+------------------+------------------------+--------------------------------------------------------------------------+
| a->values()      | array_values(a)        | Return all the values of an array                                        |
+------------------+------------------------+--------------------------------------------------------------------------+
| a->split()       | array_chunk(a)         | Split an array into chunks                                               |
+------------------+------------------------+--------------------------------------------------------------------------+
| a->combine(b)    | array_combine(a, b)    | Creates an array by using one array for keys and another for its values  |
+------------------+------------------------+--------------------------------------------------------------------------+
| a->intersect(b)  | array_intersect(a, b)  | Computes the intersection of arrays                                      |
+------------------+------------------------+--------------------------------------------------------------------------+
| a->merge(b)      | array_merge(a, b)      | Merge one or more arrays                                                 |
+------------------+------------------------+--------------------------------------------------------------------------+
| a->pad()         | array_pad(a, b)        | Pad array to the specified length with a value                           |
+------------------+------------------------+--------------------------------------------------------------------------+

Char
^^^^
The following char built-in methods are available:

+--------------+--------------------+
| OO           | Procedural         |
+--------------+--------------------+
| ch->toHex()  | sprintf("%X", ch)  |
+--------------+--------------------+

Integer
^^^^^^^
The following integer built-in methods are available:

+------------+-------------+
| OO         | Procedural  |
+------------+-------------+
| i->abs()   | abs(i)      |
+------------+-------------+
