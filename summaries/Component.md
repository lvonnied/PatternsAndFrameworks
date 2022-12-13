# Component 

## Context 
When programming a video game, it is important to consider how we want to split/inherit the code in order to have reusability without duplication. 

## Problem 
How can we split the code without having duplication or multiple inheritance. 

## Forces 
- Reusability 
- Inheritance 
- (Code splitte nach usage) 
- Maintainability 

## Solution 
The solution to our problem is components. Instead of using inheritance like in most cases of OO programming we use components in which each component holds its respective code (e.g physics, rendering, etc.) and a single entity, the game object, holds those components. This is useful for cases in which a class touches multiple domains but the domains themselves should be decoupled from each other. This declutters classes that are getting massive but still enables the possiblity of sharing capabilities of classes without using inheritance. 

## Example 
Let’s assume we have a game object class which uses “components” like input, physics and graphics. We want to extract those “components” into separate classes. We also don’t want that the game object knows the exact implementation of those components so we create an interface and the game object only holds a pointer to those components without having an inline instance. 
Here is a simple example of the input component: 

*Interface InputComponent*
```c++
class InputComponent {
    public:
        virtual ~InputComponent() {}
        virtual void update(GameObject& gameObject) = 0;
};
```

*PlayerInputComponent implementing the interface*
```c++
class PlayerInputComponent: public InputComponent {
    public:
        virtual void update(GameObject& gameObject) {
            switch (Controller::getJoystickDirection()) {
                case DIR_LEFT:
                    bjorn.velocity -= WALK_ACCELARATION;
                    break;
                case DIR_RIGHT:
                    bjorn.velocity += WALK_ACCELERATION;
                    break;
            }
        }
    private:
        static const int WALK_ACCELERATION = 1;
}
```

*Class GameObject using implementations of the different interfaces*
```c++
class GameObject {
    public:
        int velocity;
        int x, y;

    Bjorn(InputComponent* input,
          PhysicsComponent* physics,
          GraphicsComponent* graphics)
    : input_(input),
      physics_(physisc),
      graphics_(graphics)
    {}

    void update(World& world, Graphics& graphics) {
        input_->update(*this);
        physics_->update(*this, world);
        graphics_->update(*this, graphics);
    }

    private:
        InputComponent* input_;
        PhysicsComponent physics_;
        GraphicsComponent graphics_;
}
```

## Questions 
<details>
    <summary>
        Do component classes know about each other?
    </summary>
    No
</details>

<details>
    <summary>
        Deadly diamonds only occur in class hierarchies with multiple inheritances two paths leading to the same base class.
    </summary>
    Yes
</details>