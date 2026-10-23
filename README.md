# Scheduler

Simple 1Khz timer(timer0) based cooperative scheduler for the atmega328p chip in the Arduino Nano and Uno.  
The tone library also uses timer0 so using this scheduler may cause issues

## Usage

Add tasks to the scheduler

```bat
#include "Scheduler.h"
#include "SomeClass.h"

#define HZ 1000

Scheduler scheduler;

SomeClass someClassInstance;

// Scheduler task signature
void someGlobalFunc(void* ctx)
{
    (void)ctx;  // Discard context since since this is a global function

    // Do something
}

int someStaticFuncInSomeClassTaskID;
int someGlobalFuncTaskID;

void setup()
{
    // someStaticFuncInSomeClass has to be a static function that takes a single void* arg and returns void
    // An explicit type conversion can be used to call a member function of SomeClass
    // e.g. static_cast<SomeClass*>(ctx)->someMemberFunctionInSomeClass();
    someStaticFuncInSomeClassTaskID = scheduler.addTask(&SomeClass::someStaticFuncInSomeClass, &someClassInstance, HZ);

    // Globally defined functions can be used directly, but they still need to satisfy the task signature
    someGlobalFuncTaskID = scheduler.addTask(someGlobalFunc, nullptr, HZ);
}

void loop()
{
    // Run all tasks in scheduler task array cooperatively
    // DO NOT CALL A BLOCKING FUNCTION LIKE DELAY()
    scheduler.run();
}
```