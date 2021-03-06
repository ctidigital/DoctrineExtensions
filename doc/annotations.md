# Annotation reference

Bellow you will find all annotation descriptions used in these extensions.
There will be introduction on usage with examples. For more detailed usage
on extensions, refer to their specific documentation.

Content:
    
- [Tree](#tree)
- [Translatable](#translatable)
- [Sluggable](#sluggable)
- [Timestampable](#timestampable)
- [Loggable](#loggable)

## Tree annotations {#tree}

Tree can use diferent adapters. Currently **Tree** extension supports **NestedSet**
and **Closure** strategies which has a difference for annotations used.

### @gedmo:Tree (required for all tree strategies)

**class** annotation

Is the main identificator of tree used for domain object which should **act as Tree**.

**options:**

- **type** - (string) _optional_ default: **nested**

example:

    /**
     * @gedmo:Tree(type="nested")
     * @Entity(repositoryClass="Gedmo\Tree\Entity\Repository\NestedTreeRepository")
     */
    class Category ...

### @gedmo:TreeParent (required for all tree strategies)

**property** annotation

This annotation forces to specify the **parent** field, which must be a **ManyToOne**
relation.

example:

    /**
     * @gedmo:TreeParent
     * @ManyToOne(targetEntity="Category")
     * @JoinColumn(name="parent_id", referencedColumnName="id", onDelete="SET NULL")
     */
    private $parent;

### @gedmo:TreeLeft (required for nested tree)

**property** annotation

This annotation forces to specify the **left** field, which will be used for generation
of nestedset left values. Property must be **integer** type.

example:

    /**
     * @gedmo:TreeLeft
     * @Column(type=integer)
     */
    private $lft;

### @gedmo:TreeRight (required for nested tree)

**property** annotation

This annotation forces to specify the **right** field, which will be used for generation
of nestedset right values. Property must be **integer** type.

example:

    /**
     * @gedmo:TreeRight
     * @Column(type=integer)
     */
    private $rgt;

### @gedmo:TreeRoot (optional for nested tree)

**property** annotation

This annotation will use **integer** type field to specify the root of tree. This way
updating tree will cost less because each root will act as separate tree.

example:

    /**
     * @gedmo:TreeRoot
     * @Column(type=integer, nullable=true)
     */
    private $root;

### @gedmo:TreeLevel (optional for nested tree)

**property** annotation

This annotation lets to store the **level** of each node in the tree, in other word it
is depth. Can be used for indentation for instance. Property must be **integer** type.

example:

    /**
     * @gedmo:TreeLevel
     * @Column(type=integer)
     */
    private $lvl;

### @gedmo:TreeClosure (required for closure tree)

**class** annotation

This annotation forces to specify the closure domain object, which must
extend **AbstractClosure** in order to have personal closures.

**options:**

- **class** - (string) _required_

example:

    /**
     * @gedmo:Tree(type="closure")
     * @gedmo:TreeClosure(class="Entity\CategoryClosure")
     * @Entity(repositoryClass="Gedmo\Tree\Entity\Repository\ClosureTreeRepository")
     */
    class Category ...

### @gedmo:TreeChildCount (optional for closure tree)

**property** annotation

This annotation will use **integer** field to store count of children for
each node.

example:

    /**
     * @gedmo:TreeChildCount
     * @Column(name="child_count", type=integer)
     */
    private $childCount;

## Translatable annotations {#translatable}

Translatable additionaly can have unmapped property, which would override the
locale used by listener.

### @gedmo:TranslationEntity (optional)

**class** annotation

This class annotation can force translatable to use **personal Entity** to store
translations. In large tables this can be very handy.

**options:**

- **class** - (string) _required_

example:

    /**
     * @gedmo:TranslationEntity(class="Entity\ProductTranslation")
     * @Entity
     */
    class Product ...

### @gedmo:Translatable (required in order to translate)

**property** annotation

This annotation simply marks **any type** of field to be tracked and translated into
currently used locale. Locale can be forced through entity or set by **TranslationListener**.

example:

    /**
     * @gedmo:Translatable
     * @Column(type=text)
     */
    private $content;
    
### @gedmo:Locale or @gedmo:Language (optional)

**unmapped property** annotation

Both annotations will do exactly the same - mark property as one which can override
the locale set by **TranslationListener**. Property must not be mapped, that means
it cannot be stored in database.

example:

    /**
     * @gedmo:Locale
     */
    private $locale;

## Sluggable annotations {#sluggable}

Sluggable ensures unique slugs and correct length of the slug. It also uses utf8 to ascii
table map to create correct ascii slugs.

### @gedmo:Sluggable (required at least one sluggable field)

**property** annotation

Includes the marked **string** type property into generation of slug. Can be built from diferent fields,
**position** option specifies the order in slug concatenation

**options:**

- **position** - (integer) _optional_ default: **0**

example:

    /**
     * @gedmo:Sluggable
     * @Column(length=64)
     */
    private $code;

### @gedmo:Sluggable (required)

**property** annotation

It will use this **string** property to store the generated slug.

**options:**

- **updatable** - (boolean) _optional_ default: **true**
- **separator** - (string) _optional_ default: **-**
- **unique** - (boolean) _optional_ default: **true**
- **style** - (string) _optional_ default: **default** lowercase, can be **camel** also

example:

    /**
     * @gedmo:Slug
     * @Column(length=64, unique=true)
     */
    private $slug;

## Timestampable annotations {#timestampable}

Timestampable will update date fields on create, update or property change. If you set/force
date manualy it will not update it.

### @gedmo:Timestampable (required)

Marks a **date, datetime or time** field as timestampable.

**options:**

- **on** - (string) _optional_ default: **update**, other choise is **create** or **change**
- **field** - (string) _conditional_ required only if it triggers on **change**, name of the **field**
or if it is a relation **property.field**
- **value** - (mixed) _conditional_ required only if it triggers on **change**, value of property
which would trigger an update.

example:

    /**
     * @gedmo:Timestampable(on="create")
     * @Column(type="datetime")
     */
    private $created;
    
    /**
     * @gedmo:Timestampable(on="change", field="status.title", value="Published")
     * @Column(type="date")
     */
    private $published;
    
    /**
     * @ManyToOne(targetEntity="Status")
     */
    private $status;

