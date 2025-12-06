# Computer-Networks-Project
A communication project in C++ between Client and Server, demonstarting basic socket programming.

1. thread - The Main Concurrency Library
Here's what it does in your code:

In Server:
- std::thread udp_thread(udp_heartbeat_listener) - Creates a separate thread to continuously listen for UDP heartbeats from all campuses without blocking the main thread
- std::thread admin_thread(admin_console) - Runs the admin menu in its own thread so admins can interact while clients connect
- std::thread th(handle_client, client_socket) - Spawns a NEW thread for EACH client connection, allowing multiple campuses to connect and communicate simultaneously

In Client:
- std::thread recv_thread(receive_messages) - Continuously listens for incoming messages from server without blocking user input
- std::thread hb_thread(send_heartbeat) - Sends periodic heartbeats every 5 seconds in background
- std::thread udp_announce_thread(receive_udp_announcements) - Listens for admin broadcasts independently

Key Method: .detach()
- Separates the thread from the main program
- Thread runs independently in the background
- Main program doesn't wait for it to finish

2. mutex - The Synchronization Library
This prevents race conditions  (multiple threads accessing shared data simultaneously and corrupting it).

std::mutex clients_mutex - A lock that protects the shared campus_clients map

How it works:
cpp
{
    std::lock_guard<std::mutex> lock(clients_mutex);
    // Only ONE thread can be inside this block at a time
    campus_clients[campus] = ...;  // Safe to modify
} // Lock automatically releases here

Why it's needed:
- Multiple client threads might try to add/remove/read from campus_clients at the same time
- Without mutex: Data corruption, crashes, or lost updates
- With mutex: Threads wait their turn (serialized access to shared data)

3. atomic - Lock-Free Synchronization
std::atomic<bool> running(true) in the client code

- Provides thread-safe boolean flag without needing a mutex
- Multiple threads can safely read/write this variable
- Hardware-level atomic operations (faster than mutex for simple types)
- Used to signal all threads when to stop (clean shutdown)
