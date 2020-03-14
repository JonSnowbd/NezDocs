---
layout: default
title: Collision
parent: Features
nav_order: 1
---

Nez features a complete kit to roll your games unique physics as easily as possible. This is generally
much more desirable than full physics implementation(which is also available if needed) for platformers
or other such games that require tight controls and deterministic physics that are less likely to
have unintended behaviour emerge.

----

## Overlap Detection API

Nez includes a static class that has methods for detecting overlaps in the spatial hash. This is
generally very fast, and check culling is handled for you.

[Sourcefile](https://github.com/prime31/Nez/blob/master/Nez.Portable/Physics/Physics.cs#L122)

```csharp
// Returns the first encountered Collider that the circle hits.
CollisionResult x = Physics.OverlapCircle(Position, Radius) 

// Returns an int for use in a for loop, that tells you how many 
// Colliders were encountered. Results is a Collider[] array that is written to.
int x = Physics.OverlapCircleAll(Position, Radius, Results) 

// Returns the first encountered Collider that the rect hits.
CollisionResult x = Physics.OverlapRectangle(ref RectangleF)

// Returns an int for use in a for loop, that tells you how many Colliders
// were encountered. Results is a Collider[] array that is written to.
int x = Physics.OverlapRectangleAll(ref RectangleF, Results) 
```

All of these can take an optional int which represents a LayerMask to filter what can and cannot return
a collision.

----

## Mover Example

When you are making an entity that can move and should respect collisions, its recommended
to use the `Mover` component. It's versatile and simple enough to not be restrictive in how
you design your physics interactions.

Here is an example of an entity that will bounce around like a classic Pong ball.

```csharp
public class Pongball : Entity
{
    Mover Mover;
    CollisionResult Collision;
    public Vector2 Speed;

    public Pongball() : base("Pong Ball")
    {
        AddComponent(new CircleCollider(8f)); // Reference to the collider isnt required.
        Speed = new Vector2(60f, 40f);

        // Mover is a component that is very small. It only does a few things:
        // 1: .Move(Vector2, out Col) will translate the entity and check if any collider attached hits something
        // 2: Then applies the minimum translation distance to make it not clip
        // 3: sets the Out Col to represent what happened.
        Mover = AddComponent<Mover>();

        // With this in mind, its generally recommended to use this component as an 'interface' to
        // get collision legwork out of the way when moving an entity with colliders.
    }

    public override void Update()
    {
        base.Update();

        Mover.Move(Speed * Time.DeltaTime, out Collision);

        // Nullcheck .Collider to see if it collided this frame.
        if (Collision.Collider != null)
        {
            Speed = Vector2.Reflect(Speed, Collision.Normal);
        }
    }
}
```

![Pongball example]({{site.baseurl}}/assets/example/pongball.gif)

As an example of how this could integrate with gameplay, you could examine `Collision.Collider.Entity`
to see if it is, for example, a `PongGoalpost` to increment the score and reset the balls position