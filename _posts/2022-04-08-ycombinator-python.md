---
title: Y Combinator in python
date: 2022-04-08 08:00:00 -3000
categories: [Functional Programming]
tags: [python, ycombinator, computer science, functional programming]     # TAG names should always be lowercase
---

# Introduction

# Some section
Bla
## Some subsection
Bla 2
## My picture

![img-description](/assets/img/foto.jpg)
_Image Caption_

## Some Code
```python
def fix(f):
    """
    Similar to
    
    def fix(f): 
        return f( lambda *a,**kw: fix(f)(*a,**kw) )
    
    But built as a combinator (Y combinator):
    def fix(f):
        return (lambda u: u(u))(
            lambda x: f(
                lambda *a, **kw: x(x)(*a, **kw)
            )
        )
    """
    def u(x):
        def lazy(*a, **kw):
            return x(x)(*a, **kw)
        return f(lazy)
    
    return u(u)


def cached(f, cache, miss):    
    def u(*a, **kw):
        key = f'({a}, {kw})'
        r = cache.get(key, miss)
        if r is not miss:            
            return r
        r = f(*a, **kw)            
        cache[key] = r
        return r
    return u

def memo(f):
    """
    Similar to
    
    def fix(f): 
        return f( lambda *a,**kw: fix(f)(*a,**kw) )
    
    But built as a combinator (Y combinator), and using a dict as
    memoizing strategy.
    """
    cache = {}    
    miss = object()
    def u(x):
        def lazy(*a, **kw):
            return x(x)(*a, **kw)        
        return f(cached(lazy, cache, miss))
    
    return u(u)  


def fixtc(f):
    """
    Similar to
    
    def fix(f): 
        return f( lambda *a,**kw: fix(f)(*a,**kw) )
    
    But built as a combinator (Y combinator), and leveraging taill call
    optimization.
    
    """
    def u(x):
        def lazy(*a, **kw):
            return lambda : x(x)(*a, **kw)
        return f(lazy)
    
    g = u(u)
    def runtc(*a, **kw):
        u = g(*a, **kw)
        while callable(u):
            u = u()
        return u
    return runtc


def comp(f, g): 
    """
        Function composition u(x) = f(g(x))
    """
    return lambda *a,**kw: f(g(*a,**kw))

def apply(f):
    return lambda *a, **kw: f(*a, **kw)
```