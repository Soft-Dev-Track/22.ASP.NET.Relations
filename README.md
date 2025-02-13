# 22.ASP.NET : Relations
In this section, we will continue our journey with relationships in database modeling using **ASP.NET**. As we have previously seen, there are three main types of relationships between tables:

- **One-To-One (1:1)**: An entity `X` has a single related entity `Y`, and entity `Y` belongs to entity X. This type of relationship is often used when splitting a table for better organization.

- **One-To-Many (1:N)**: An entity `X` can have multiple related entities `Y`, while each entity `Y` belongs to a single entity `X`. This is the most common type of relationship in relational databases, such as a `Category` having multiple `Products`, but each `Product` belonging to only one `Category`.

- **Many-To-Many (M:N)**: An entity `X` can have multiple associated entities `Y`, and vice versa. This type of relationship requires an intermediate table (junction table) to store the associations between `X` and `Y`. For example, a `Student` can be enrolled in multiple `Courses`, and each `Course` can have multiple `Students`.

## Implementing Relationships in ASP.NET
In `ASP.NET`, we can define these relationships using **Entity Framework**, which provides two main ways to configure them:

- **Fluent API**: This approach allows defining relationships explicitly using the OnModelCreating method in the DbContext class. It provides fine-grained control over table configurations.

- **Data Annotations**: This method uses attributes in entity classes to define relationships, making it more intuitive and easier to use for simple scenarios.

For this course, let us just explore them with **Fluent API**.

### 1. One-To-One (1:1) 
In a **One-to-One (0:1)** relationship, the `primary` key of an entity can also serve as a `foreign` key. This is known as an **identifying** relationship. It is something that very common.

#### Explanation
- In a **One-to-One** relationship, the dependent entity (child) uses the same primary key as the principal entity (parent).
- The `primary` key of the **child** entity also acts as a `foreign` key, referencing the **parent** entity.

#### Example
```user```
|Name|Type|Null|Other|
|:---|:---|:---|-----|
|Id |int | NN | Primary key|
|Firstname |varchar(10) | NN |
|Lastname |varchar(10) | Null |
|Email |varchar(10) | NN |

----

```Address```
|Name|Type|Null|Other|
|:---|:---|:---|-----|
|IdUser |int | NN | Primary key|
|StreetNumber |int | NN |
|Street |varchar(20) | NN |
|Country |varchar(10) | NN |
|ZipCode |int | NN |
|Town |varchar(10) | NN |

```csharp
modelBuilder.Entity<Address>(entity => {
    entity.HasKey(a => a.IdUser);
    entity.Property(a => a.StreetNumber).HasMaxLength(5).IsRequired();
    entity.Property(a => a.Street).HasMaxLength(10).IsRequired();
    entity.Property(a => a.Town).HasMaxLength(10).IsRequired();
    entity.Property(a => a.ZipCode).IsRequired();
    entity.Property(a => a.Country).HasMaxLength(10).IsRequired();

    entity
        .HasOne<User>()
        .WithOne()
        .HasForeignKey<Address>(fk => fk.IdUser)
        .OnDelete(DeleteBehavior.NoAction);
});
```
#### Explanation
This code configures a **One-to-One** relationship between the `Address` and `User` entities in Entity Framework. 

- **HasOne<User>()** method defines that the `Address` entity has a relationship with `User.

- **WithOne()** ensures it is a `One-to-One` relationship. 

- **HasForeignKey<Address>(fk => fk.IdUser)** specifies that the foreign key in the `Address` table is `IdUser`, linking it to the User entity. 

- **OnDelete(DeleteBehavior.NoAction)** ensures that if a `User` is deleted, no automatic action is taken on the related Address, meaning the address record will remain in the database unless manually deleted.

