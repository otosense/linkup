
# linkup
Making mappings operable.

Note: This package takes no dependencies. Pure builtin python. 

If you're going to operate with large amounts of data, and can afford more computational resources, 
you may want to compare to using `pandas.Series`. 
But if you want to (and can) be light on your feet, this is the package for you. 

To install:	```pip install linkup```

# Operable Mappings

## OperableMapping

```pydocstring
>>> d = OperableMapping({'a': 8, 'b': 4})
>>> dd = OperableMapping(b=2, c=1)  # you can make one this way too
>>> d + dd
{'a': 8, 'b': 6, 'c': 1}
>>> d - dd
{'a': 8, 'b': 2, 'c': -1}
>>> d * dd
{'a': 8, 'b': 8, 'c': 1}
>>> d / dd
{'a': 8.0, 'b': 2.0, 'c': 1.0}
```


The result of the operations are themselves DictWithOps, so you can compose several.

```pydocstring
>>> d * (dd + d) / d  # notice that this is equivalent to d + dd (but with numbers cast to floats)
{'a': 8.0, 'b': 6.0, 'c': 1.0}
```

You can also use values (which will have the effect of being broadcast to all values of the mapping.

```pydocstring
>>> d + 1
{'a': 9, 'b': 5}
>>> d * 10
{'a': 80, 'b': 40}
```

## OperableMappingNoDflts


```pydocstring
>>> from linkup.base import *
>>> d = OperableMappingNoDflts({'a': 8, 'b': 4, 'c': 3})
>>> dd = OperableMappingNoDflts(b=2, c=1, d=0)  # you can make one this way too
>>>
>>> d + 1
{'a': 9, 'b': 5, 'c': 4}
>>> d / dd
{'b': 2.0, 'c': 3.0}
>>> (d + 1) / dd
{'b': 2.5, 'c': 4.0}
```

    

# Functions

If you don't need to create operable instances, you can just use a function instead. 
It's what OperableMapping uses behind the scenes, so will be faster if you use it directly
(but look less elegantly mathematical in your code).

You have a choice of functions, depending on what you need:
- ``key_aligned_val_op_with_forced_defaults``: For applying a (mapping, mapping) operation
- ``map_op_val``: For applying an operation involving a mapping and a single value. 
- ``key_aligned_val_op``: like key_aligned_val_op_with_forced_defaults, but defaults are optional. 
If not given, the result will be that if there is no item for a key of a defaultless mapping, 
that key won't show up in the result. See examples. It's easier than you think.


## key_aligned_val_op

```pydocstring
>>> from operator import add, sub, mul, truediv, and_, or_, xor
>>> x = {'a': 8, 'b': 4}
>>> y = {'b': 2, 'c': 1}

If no default vals are given, only those keys that are in both mappings will be in the output.

>>> key_aligned_val_op(x, y, add)
{'b': 6}

If we specify a default for ``x`` then all items of ``y`` can be used.

>>> key_aligned_val_op(x, y, add, dflt_val_for_x=0)
{'b': 6, 'c': 1}

If we specify a default for ``y`` then all items of ``x`` can be used.

>>> key_aligned_val_op(x, y, add, dflt_val_for_y=0)
{'a': 8, 'b': 6}
```

## map_op_val


```pydocstring
>>> from operator import add, sub, mul, truediv
>>> x = dict(a=2, b=3)
>>> map_op_val(x, 2, add)
{'a': 4, 'b': 5}
>>> map_op_val(x, 2, sub)
{'a': 0, 'b': 1}
>>> map_op_val(x, 2, mul)
{'a': 4, 'b': 6}
>>> map_op_val(x, 2, truediv)
{'a': 1.0, 'b': 1.5}
```

## key_aligned_val_op_with_forced_defaults

Really, does exactly what ``key_aligned_val_op``, but defaults are not optional, 
which allows it to be faster.

```pydocstring
>>> from operator import add, sub, mul, truediv, and_, or_, xor
>>> x = {'a': 8, 'b': 4}
>>> y = {'b': 2, 'c': 1}
>>> key_aligned_val_op_with_forced_defaults(x, y, add, 0, 0)
{'a': 8, 'b': 6, 'c': 1}
>>> key_aligned_val_op_with_forced_defaults(x, y, sub, 0, 0)
{'a': 8, 'b': 2, 'c': -1}
>>> key_aligned_val_op_with_forced_defaults(x, y, mul, 1, 1)
{'a': 8, 'b': 8, 'c': 1}
>>> key_aligned_val_op_with_forced_defaults(x, y, truediv, 1, 1)
{'a': 8.0, 'b': 2.0, 'c': 1.0}
>>> x = {'a': [8], 'b': [4]}
>>> y = {'b': [2], 'c': [1]}
>>> key_aligned_val_op_with_forced_defaults(x, y, add, [], [])
{'a': [8], 'b': [4, 2], 'c': [1]}
>>> x = {'a': True, 'b': False}
>>> y = {'b': True, 'c': False}
>>> key_aligned_val_op_with_forced_defaults(x, y, and_, True, True)
{'a': True, 'b': False, 'c': False}
>>> key_aligned_val_op_with_forced_defaults(x, y, or_, True, True)
{'a': True, 'b': True, 'c': True}
>>> key_aligned_val_op_with_forced_defaults(x, y, xor, True, True)
{'a': False, 'b': True, 'c': True}
```
