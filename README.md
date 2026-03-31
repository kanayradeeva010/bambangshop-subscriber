# BambangShop Receiver App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create SubscriberRequest model struct.`
    -   [ ] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Notification repository.`
    -   [ ] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Commit: `Implement receive_notification function in Notification service.`
    -   [ ] Commit: `Implement receive function in Notification controller.`
    -   [ ] Commit: `Implement list_messages function in Notification service.`
    -   [ ] Commit: `Implement list function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1
# Question -1
In the receiver app, the NOTIFICATION vector is a global static variable, multiple threads might try to access this vector at the exact same time, without a synchronization like RwLock, this would cause a data race which leads to memory corruption or program crash. The RwLock is intended to ensure memory safety. Why do we use RwLock instead of mutex is that RwLock allows multiple threads to read data simultaneously, in a typical notification app, read operations happen frequently. Also, a write operation requires an exclusive access, when a thread is writing, all other readers and writers are blocked till the write is finished. If we used a mutex, the system would be much slower because mutex only have one lock for all kinds of operations.  

# Question -2
Unlke java, rust prohibits direct mutation of static variables because it breaks the compiler's ability to ensure memory safety accross multiple threads. 

A static variable allows any thread to modify them directly, it would bypass rust's strict ownership and borrowing rules. To mantain it's safety guarantees rust forces us to wrap mutable global state in synchronization primitives like Mutex or RwLock, and lazy_static allows us to intialize them at runtme since rust cannot evaluate complex expressions at compile time for static variables. This ensures the global data is always accessed through a controlled locking mechanism, making the code thread safe by design rather than relying on the programmer to avoid mistakes. 

#### Reflection Subscriber-2
# Question -1
I explored src/lib.rs and learned that it's basically the brain of the app's configuration. Things that caught my eye is :
- AppConfig & Figment 
It lets us change things like the instance name or port via a .env without touching the code, it makes running multiple instances such as 8001, 8002, etc super easy

- lazy_static! for REQWEST_CLIENT
in rust we can't just make a global http client easily, using lazy_static ensures the client is a singleton, created once and reused, which is much better for performance than opening a new connection every time. 

# Question -2
the observer pattern makes the system feel like "lego." it enable us to spawn 10 more receivers on different ports, and the main app wouldn't care. it just adds their URLs to the list and sends notifications, no code changes needed.

even if we had multiple Publishers, the system stays flexible, a receiver would just need to "register" itself to each publisher. Since they communicate via APIs, they don't depend on each other's internal logic.

# Question -3
The postman collection helped me for my workflow, instead of manually hittng endpoints or copy pasting JSON payloads everytime, i can just click and test the whole flow (example subscribe -> notify -> list) in seconds. For group projects, having a shared Postman collection means everyone tests with the same request format, reducing miscommunication about endpoint behavior. It makes collaboration way faster in a group work. 

