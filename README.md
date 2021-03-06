# session12_EPAI3_assignment

# **Session 12 - Iterables and Iterators - II**

## **Iterables and Iterators**

* An iterable is anything that you can get an iterator from using `iter`

* An iterator is an iterable that you can loop over using `next`

### **Project: Description**

1. The starting point for this project is the Polygon class and the Polygons sequence type we created in the previous project.

2. The code for these classes along with the unit tests for the Polygon class are below if you want to use those as your starting point. But use whatever you came up with in the last project.

**We have two goals:**

### **Goal 1**

* Refactor the Polygon class so that all the calculated properties are lazy properties, i.e. they should still be calculated properties, but they should not have to get recalculated more than once (since we made our Polygon class "immutable").


```python
class Polygon:
    def __init__(self, n, R):
        if n < 3:
            raise ValueError('Polygon must have at least 3 vertices.')
        self._n = n
        self._R = R
        self._interior_angle = None
        self._side_length = None
        self._apothem = None
        self._area = None
        self._perimeter = None
        
    def __repr__(self):
        return f'Polygon(n={self._n}, R={self._R})'
    
    @property
    def count_vertices(self):
        return self._n
    
    @property
    def count_edges(self):
        return self._n
    
    @property
    def circumradius(self):
        return self._R
    
    @property
    def interior_angle(self):
        if self._interior_angle is None:
            self._interior_angle = (self._n - 2) * 180 / self._n
        return self._interior_angle

    @property
    def side_length(self):
        if self._side_length is None:
            self._side_length = 2 * self._R * math.sin(math.pi / self._n)
        return self._side_length
    
    @property
    def apothem(self):
        if self._apothem is None:
            self._apothem = self._R * math.cos(math.pi / self._n)
        return self._apothem
    
    @property
    def area(self):
        if self._area is None:
            self._area = self._n / 2 * self.side_length * self.apothem
        return self._area
    
    @property
    def perimeter(self):
        if self._perimeter is None:
            self._perimeter = self._n * self.side_length
        return self._perimeter
    
    def __eq__(self, other):
        if isinstance(other, self.__class__):
            return (self.count_edges == other.count_edges 
                    and self.circumradius == other.circumradius)
        else:
            return NotImplemented
        
    def __gt__(self, other):
        if isinstance(other, self.__class__):
            return self.count_vertices > other.count_vertices
        else:
            return NotImplemented
```

### **Goal 2**

* Refactor the Polygons (sequence) type, into an iterable. Make sure also that the elements in the iterator are computed lazily - i.e. you can no longer use a list as an underlying storage mechanism for your polygons.

* You'll need to implement both an iterable, and an iterator.

```python
class PolyIterator:
    def __init__(self, m, R):
        if m < 3:
            raise ValueError('m must be greater than 3')
        self._m = m
        self._R = R
        self._i = 3
        
    def __iter__(self):
        return self
    
    def __next__(self):
        if self._i > self._m:
            raise StopIteration
        else:
            result = Polygon(self._i, self._R)
            self._i += 1
            return result  
```

```python
class Polygons:
    ''' 
    Lazy calculation done for max_efficiency_polygon function
    '''
    def __init__(self, m, R):
        if m < 3:
            raise ValueError('m must be greater than 3')
        self._m = m
        self._R = R
        self._max_efficiency_polygon = None
        
    def __len__(self):
        return self._m - 2
    
    def __repr__(self):
        return f'Polygons(m={self._m}, R={self._R})'
    
    def __iter__(self):
        return PolyIterator(self._m, self._R)
    
    @property
    def max_efficiency_polygon(self):
        if self._max_efficiency_polygon is None:
            sorted_polygons = sorted(PolyIterator(self._m, self._R), 
                                     key=lambda p: p.area/p.perimeter,
                                    reverse=True)
            self._max_efficiency_polygon = sorted_polygons[0]
        return self._max_efficiency_polygon
```


### **Test cases:**

* `__repr__` 

  Returns a string containing a printable representation of an object.

* `__eq__`

  Python automatically calls the `__eq__` method of a class when you use the == operator to compare the instances of the class.

* `__gt__`

  It performs rich comparisons between a and b. 

  gt(a, b) is equivalent to a > b 


* `__len__`
  Returns length of an object.

* `__iter__`
  An iterable object is an object that implements `__iter__`, which is expected to return an iterator object. 

* `__next__`
  An iterator is an object that implements `__next__`, which is expected to return the next element of the iterable object that returned it, and raise a StopIteration exception when no more elements are available.
  



