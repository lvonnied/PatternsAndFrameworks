# Event Queue Pattern

## Context
We want to programm video games with events and notifications.
For example after defeating an enemy we want to play a sound to indicate that the enemy has fallen.

## Problem
How can we receieve and process events without freezing the game.

## Forces

- Decoupling
- Request / Return
- Events
- Time

## Solution

The solution is Event Queue Patterns.
When an enemy has been defeated, we want the game to send an event indicating it needs a sound to be played.
The difference to other event patterns like observer pattern is that the receiver pulls the event when it has time.
A good example is to think of this like pushing and pulling.
When A initiates something it pushes a request to B.
B will process that event by pulling the request when it is ready.
In order to not lose any events that occured, B will implement a Queue (e.g an array) to store those events.
These events can be implemented in different ways like priority or as a ring buffer.

## Example

Structure of the sound to be played
```c#
struct PlayMessage {
    SoundId id;
    int volume;
}
```

Audio class implementing a ring buffer to process sounds in the queue
```c#
class Audio {
    public:
        static void init() {
            head_ = 0;
            tail_ = 0;
        }

    private:
        static int head_;
        static int tail_;
        static const int MAX_PENDING = 16;

        // ...
}
```

PlaySound method which checks if MAX_PENDING has been reached and if not adds element to the tail
```c#
void Audio::playSound(SoundId id, int volume) {
            assert((tail_ + 1) % MAX_PENDING != head_);

            // Add sound to the end of list..
            pending_[tail_].id = id;
            pending_[tail_].volume = volume;
            tail_ = (tail_ + 1) % MAX_PENDING;
        }
```

Update method which plays all sounds in the queue
```c#
void Audio::update() {
            // Return if no pending requests
            if(head_ == tail_) return;

            ResourceId resource = loadSound(pending_[head_].id);
            int channel = findOpenChannel();
            if(channel == -1) return;
            startSound(resource, channel, pending_[head_].volume);

            head_ = (head_ + 1) % MAX_PENDING;
        }
```

## Questions 
<details>
    <summary>
        Components receive their events by pulling instead of getting notified by the sender.
    </summary>
    Yes
</details>

<details>
    <summary>
        An array is better suited for a queue than a ring buffer because it is less complex.
    </summary>
    No
</details>
