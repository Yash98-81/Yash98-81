#include<iostream> 
#include<thread> 
 
using namespace std; 
 
bool chopsticks[5] = { true, true, true, true, 
true }; // true means available 
 
void philosopher(int id) { 
    while (true) { 
        int left = id; 
        int right = (id + 1) % 5; 
         
        // Try to pick up chopsticks 
        if (chopsticks[left] && chopsticks[right]) { 
            chopsticks[left] = false; // pick left 
            chopsticks[right] = false; // pick right 
             
            cout << "Philosopher " << id << " is 
eating." << endl; 
            
this_thread::sleep_for(chrono::seconds(1)); // 
eating 
             
            chopsticks[left] = true; // release left 
            chopsticks[right] = true; // release right 
             
            cout << "Philosopher " << id << " 
finished eating and starts thinking." << endl; 
            
this_thread::sleep_for(chrono::seconds(1)); // 
thinking 
        } 
    } 
} 
 
int main() { 
    thread philosophers[5]; 
     
    for (int i = 0; i < 5; ++i) { 
        philosophers[i] = thread(philosopher, i); 
    } 
     
    for (int i = 0; i < 5; ++i) { 
        philosophers[i].join(); 
    } 
 
    return 0; 
} 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
#include<iostream> 
#include<thread> 
#include<queue> 
 
using namespace std; 
 
queue<int> buffer; 
const int BUFFER_SIZE = 10; 
 
void producer() { 
    int item = 0; 
    while (true) { 
        if (buffer.size() < BUFFER_SIZE) { 
            buffer.push(item); 
            cout << "Producer produced item " << 
item << endl; 
            item++; 
            
this_thread::sleep_for(chrono::seconds(1)); // 
Simulate time to produce 
        } 
    } 
} 
 
void consumer() { 
    while (true) { 
        if (!buffer.empty()) { 
            int item = buffer.front(); 
            buffer.pop(); 
            cout << "Consumer consumed item " << 
item << endl; 
            
this_thread::sleep_for(chrono::seconds(1)); // 
Simulate time to consume 
        } 
    } 
} 
 
int main() { 
    thread prod(producer); 
    thread cons(consumer); 
     
    prod.join(); 
    cons.join(); 
     
    return 0; 
} 
  
#include<iostream> 
#include<thread> 
 
using namespace std; 
 
int read_count = 0; 
bool writing = false; 
 
void reader(int id) { 
    while (true) { 
        if (!writing) { 
            read_count++; 
            cout << "Reader " << id << " is reading. 
Total readers: " << read_count << endl; 
            
this_thread::sleep_for(chrono::seconds(1)); // 
Simulate reading time 
             
            read_count--; 
            cout << "Reader " << id << " finished 
reading." << endl; 
        } 
        
this_thread::sleep_for(chrono::seconds(1)); // 
Simulate thinking time 
    } 
} 
void writer(int id) { 
    while (true) { 
        if (read_count == 0 && !writing) { 
            writing = true; 
            cout << "Writer " << id << " is writing." 
<< endl; 
            
this_thread::sleep_for(chrono::seconds(1)); // 
Simulate writing time 
             
            writing = false; 
            cout << "Writer " << id << " finished 
writing." << endl; 
        } 
        
this_thread::sleep_for(chrono::seconds(1)); // 
Simulate thinking time 
    } 
} 
 
int main() { 
    thread readers[3], writers[2]; 
     
    for (int i = 0; i < 3; ++i) { 
        readers[i] = thread(reader, i); 
    } 
    for (int i = 0; i < 2; ++i) { 
        writers[i] = thread(writer, i); 
    } 
     
    for (int i = 0; i < 3; ++i) { 
        readers[i].join(); 
    } 
    for (int i = 0; i < 2; ++i) { 
        writers[i].join(); 
    } 
     
    return 0; 
}
