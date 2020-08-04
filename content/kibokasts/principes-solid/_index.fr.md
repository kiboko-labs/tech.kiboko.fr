---
title: "Principes SOLID"
date: 2020-03-26
type : "docs"
---

* Single responsibility principle
* Open/closed principle
* Liskov substitution principle
* Interface segregation principle
* Dependency inversion principle


Single responsibility principle
---

```php
<?php

class Product
{
// ...
    public function getURL(ObjectManager $om): URL
    {
        $url = new URL();
        $url->setProduct($this);
        $url->setPath(...);
        $om->persist($url);
        $om->flush();
    }
// ...
}
```

Open/closed principle
--- 

```php
<?php

final class Foo
{
// ...
    public function lorem(ObjectManager $om, ?string $name = null): URL
    {
    }
// ...
}
```

Liskov substitution principle
---

```php
<?php

class Foo
{
    public function lorem()
    {
        return 42;
    }
}

final class Bar extends Foo
{
    private Registry $doctrine;
    private Repository $repository;

    public function lorem(string $path)
    {
        $this->repository->load(72)
        return new URL($path);
    }
}
```

```php
<?php

class Foo
{
    public function lorem()
    {
        return 42;
    }
}

final class Bar extends Foo
{
    public function lorem()
    {
        return 64;
    }
    
    public function ipsum(string $path)
    {
        return new URL($path);
    }
}
```

Interface segregation principle
---

```php
<?php

interface LoremInterface extends \Countable, \Traversable {}

class Lorem implements LoremInterface, \Stringable, \IteratorAggregate {}

class Ipsum implements LoremInterface, \Stringable, \Iterator {}

```

* [Traversable](https://www.php.net/manual/fr/class.traversable.php)
* [Iterator](https://www.php.net/manual/fr/class.iterator.php)
* [IteratorAggregate](https://www.php.net/manual/fr/class.iteratoraggregate.php)
* [Countable](https://www.php.net/manual/fr/class.countable.php)

Dependency inversion principle
---

```php
<?php

final class Incorrect
{
    public function lorem(EntityManager $em)
    {
        $em->persist(...);
        $em->flush();
    }
    
    public function ipsum(ArrayCollection $list)
    {
        return count($list) > 10;
    }
}
```

```php
<?php

final class Correct
{
    public function lorem(ObjectManager $om)
    {
        $om->persist(...);
        $om->flush();
    }
    
    public function ipsum(\Countable $list)
    {
        return count($list) > 10;
    }
}
```