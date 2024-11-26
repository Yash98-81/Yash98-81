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





//page replacement
#include<iostream> 
#include<vector> 
#include<algorithm> 
using namespace std; 
// Function to check if a page exists in the 
frame 
bool isPageInFrame(int page, vector<int> 
&frame) { 
    return find(frame.begin(), frame.end(), 
page) != frame.end(); 
} 
// Function to print the current state of frames 
void printFrame(vector<int> &frame) { 
    for (int i = 0; i < frame.size(); i++) { 
        if (frame[i] == -1) cout << "- "; 
        else cout << frame[i] << " "; 
    } 
    cout << endl; 
} 
// FIFO Page Replacement Algorithm 
void FIFO(vector<int> pages, int frameSize) { 
    vector<int> frame(frameSize, -1); // 
Initialize frame with -1 
    int index = 0; // Track the oldest page 
    int pageFaults = 0; 
    cout << "FIFO Page Replacement:\n"; 
    for (int page : pages) { 
        if (!isPageInFrame(page, frame)) { 
            frame[index] = page; 
            index = (index + 1) % frameSize; // 
Circular increment for FIFO 
            pageFaults++; 
            cout << "Page " << page << " loaded: "; 
        } else { 
            cout << "Page " << page << " already in 
frame: "; 
        } 
        printFrame(frame); 
    } 
    cout << "Total page faults: " << pageFaults 
<< endl << endl; 
} 
// LRU Page Replacement Algorithm 
void LRU(vector<int> pages, int frameSize) { 
    vector<int> frame(frameSize, -1); // 
Initialize frame with -1 
    vector<int> lastUsed(frameSize, -1); // Track 
last usage of each page 
    int pageFaults = 0; 
    cout << "LRU Page Replacement:\n"; 
    for (int i = 0; i < pages.size(); i++) { 
        int page = pages[i]; 
        if (!isPageInFrame(page, frame)) { 
            int lruIndex = distance(lastUsed.begin(), 
min_element(lastUsed.begin(), 
lastUsed.end())); 
            frame[lruIndex] = page; 
            pageFaults++; 
            cout << "Page " << page << " loaded: "; 
        } else { 
            cout << "Page " << page << " already in 
frame: "; 
        } 
        // Update last used time 
        lastUsed[find(frame.begin(), frame.end(), 
page) - frame.begin()] = i; 
        printFrame(frame); 
    } 
    cout << "Total page faults: " << pageFaults 
<< endl << endl; 
} 
 
// Optimal Page Replacement Algorithm 
void Optimal(vector<int> pages, int frameSize) 
{ 
    vector<int> frame(frameSize, -1); // 
Initialize frame with -1 
    int pageFaults = 0; 
 
    cout << "Optimal Page Replacement:\n"; 
    for (int i = 0; i < pages.size(); i++) { 
        int page = pages[i]; 
        if (!isPageInFrame(page, frame)) { 
            if (find(frame.begin(), frame.end(), -1) 
!= frame.end()) { 
                // If there is an empty space, use it 
                *find(frame.begin(), frame.end(), -1) 
= page; 
            } else { 
                // Find the page to replace based on 
future use 
                vector<int> future(frameSize, 
pages.size()); // Default future usage to 
beyond end 
                for (int j = 0; j < frameSize; j++) { 
                    for (int k = i + 1; k < pages.size(); 
k++) { 
                        if (frame[j] == pages[k]) { 
                            future[j] = k; 
                            break; 
                        } 
                    } 
                } 
                // Replace the page that will be used 
furthest in the future 
                int replaceIndex = 
distance(future.begin(), 
max_element(future.begin(), future.end())); 
                frame[replaceIndex] = page; 
            } 
            pageFaults++; 
            cout << "Page " << page << " loaded: "; 
        } else { 
            cout << "Page " << page << " already in 
frame: "; 
        } 
        printFrame(frame); 
    } 
    cout << "Total page faults: " << pageFaults 
<< endl; 
} 
 
int main() { 
    vector<int> pages = {7, 0, 1, 2, 0, 3, 0, 4, 2, 
3, 0, 3, 2}; 
    int frameSize = 3; 
 
    FIFO(pages, frameSize); 
    LRU(pages, frameSize); 
    Optimal(pages, frameSize); 
 
    return 0; 
} 
 



//allocation process
#include<iostream> 
#include<vector> 
 
using namespace std; 
 
// Display blocks for a file 
void displayFileBlocks(const string& filename, 
const vector<int>& blocks) { 
    cout << "File " << filename << " occupies 
blocks: "; 
    for (int block : blocks) { 
        cout << block << " "; 
    } 
    cout << endl; 
} 
 
// Continuous Allocation 
void continuousAllocation(const string& 
filename, vector<int>& disk, int startBlock, int 
length) { 
    cout << "Continuous Allocation for file " << 
filename << ":\n"; 
    vector<int> fileBlocks; 
    bool allocated = true; 
 
    for (int i = startBlock; i < startBlock + length; 
i++) { 
        if (i >= disk.size() || disk[i] != -1) { 
            allocated = false; 
            break; 
        } 
    } 
 
    if (allocated) { 
        for (int i = startBlock; i < startBlock + 
length; i++) { 
            disk[i] = 1; 
            fileBlocks.push_back(i); 
        } 
        displayFileBlocks(filename, fileBlocks); 
    } else { 
        cout << "File " << filename << " could not 
be allocated contiguously.\n"; 
    } 
} 
 
// Linked Allocation 
struct LinkedBlock { 
    int blockNumber; 
    LinkedBlock* next; 
}; 
 
void linkedAllocation(const string& filename, 
vector<int>& disk, const vector<int>& blocks) 
{ 
    cout << "Linked Allocation for file " << 
filename << ":\n"; 
    LinkedBlock* head = nullptr; 
    LinkedBlock* current = nullptr; 
     
    for (int block : blocks) { 
        if (block >= disk.size() || disk[block] != -1) 
{ 
            cout << "Block " << block << " is 
unavailable.\n"; 
            return; 
        } 
    } 
 
    for (int block : blocks) { 
        disk[block] = 1; 
        LinkedBlock* newBlock = new 
LinkedBlock{block, nullptr}; 
         
        if (!head) { 
            head = newBlock; 
            current = head; 
        } else { 
            current->next = newBlock; 
            current = newBlock; 
        } 
    } 
 
    current = head; 
    cout << "File " << filename << " occupies 
blocks (linked): "; 
    while (current) { 
        cout << current->blockNumber << " "; 
        current = current->next; 
    } 
    cout << endl; 
 
    // Free memory for the linked list 
    while (head) { 
        LinkedBlock* temp = head; 
        head = head->next; 
        delete temp; 
    } 
} 
 
// Indexed Allocation 
void indexedAllocation(const string& 
filename, vector<int>& disk, int indexBlock, 
const vector<int>& blocks) { 
    cout << "Indexed Allocation for file " << 
filename << ":\n"; 
    if (indexBlock >= disk.size() || 
disk[indexBlock] != -1) { 
        cout << "Index block " << indexBlock << " 
is unavailable.\n"; 
        return; 
    } 
 
    for (int block : blocks) { 
        if (block >= disk.size() || disk[block] != -1) 
{ 
            cout << "Block " << block << " is 
unavailable.\n"; 
            return; 
        } 
    } 
 
    disk[indexBlock] = 1; 
    vector<int> fileBlocks; 
    for (int block : blocks) { 
        disk[block] = 1; 
        fileBlocks.push_back(block); 
    } 
 
    cout << "Index block: " << indexBlock << " 
points to blocks: "; 
    displayFileBlocks(filename, fileBlocks); 
} 
int main() { 
vector<int> disk(100, -1); // -1 indicates free 
block 
// Continuous Allocation 
continuousAllocation("File1", disk, 5, 10); // 
Allocate from block 5, length 10 
// Linked Allocation 
vector<int> linkedBlocks = {20, 21, 22, 30}; 
linkedAllocation("File2", disk, linkedBlocks); 
// Allocate blocks 20, 21, 22, 30 
// Indexed Allocation 
vector<int> indexedBlocks = {40, 42, 43}; 
indexedAllocation("File3", disk, 35, 
indexedBlocks); // Index block 35 points to 
blocks 40, 42, 43 
return 0; 
}





//Disk Sceduling
#include<iostream> 
#include<vector> 
#include<algorithm> 
 
using namespace std; 
 
// Function to calculate the total seek time 
and print the seek sequence 
void calculateSeekTime(const string& 
algoName, vector<int> sequence, int head) { 
    int seekTime = 0; 
    int currentPosition = head; 
 
    cout << algoName << " Disk Scheduling:\n"; 
    cout << "Seek Sequence: "; 
    for (int req : sequence) { 
        cout << req << " "; 
        seekTime += abs(currentPosition - req); 
        currentPosition = req; 
    } 
    cout << "\nTotal Seek Time: " << seekTime 
<< endl << endl; 
} 
 
// FCFS Disk Scheduling 
void FCFS(vector<int> requests, int head) { 
    calculateSeekTime("FCFS", requests, head); 
} 
 
// SSTF (Shortest Seek Time First) Disk 
Scheduling 
void SSTF(vector<int> requests, int head) { 
    vector<int> sequence; 
    vector<bool> visited(requests.size(), false); 
    int currentPosition = head; 
 
    for (int i = 0; i < requests.size(); i++) { 
        int closestIndex = -1; 
        int minDistance = INT_MAX; 
 
        // Find the closest unvisited request 
        for (int j = 0; j < requests.size(); j++) { 
            if (!visited[j] && abs(currentPosition - 
requests[j]) < minDistance) { 
                minDistance = abs(currentPosition - 
requests[j]); 
                closestIndex = j; 
            } 
        } 
 
        if (closestIndex != -1) { 
            visited[closestIndex] = true; 
            
sequence.push_back(requests[closestIndex]); 
            currentPosition = 
requests[closestIndex]; 
        } 
    } 
 
    calculateSeekTime("SSTF", sequence, head); 
} 
 
// SCAN Disk Scheduling 
void SCAN(vector<int> requests, int head, int 
diskSize, bool direction) { 
    vector<int> left, right; 
    vector<int> sequence; 
 
    if (direction) right.push_back(diskSize - 1); 
// Moving towards the end 
    else left.push_back(0); // Moving towards 
the start 
 
    for (int req : requests) { 
        if (req < head) left.push_back(req); 
        else right.push_back(req); 
    } 
 
    sort(left.begin(), left.end()); 
    sort(right.begin(), right.end()); 
 
    if (direction) { 
        sequence.insert(sequence.end(), 
right.begin(), right.end()); 
        sequence.insert(sequence.end(), 
left.begin(), left.end()); 
    } else { 
        sequence.insert(sequence.end(), 
left.rbegin(), left.rend()); 
        sequence.insert(sequence.end(), 
right.begin(), right.end()); 
    } 
 
    calculateSeekTime("SCAN", sequence, 
head); 
} 
 
// CSCAN Disk Scheduling 
void CSCAN(vector<int> requests, int head, int 
diskSize) { 
    vector<int> left, right; 
    vector<int> sequence; 
 
    right.push_back(diskSize - 1); // End of the 
disk 
    left.push_back(0); // Start of the disk 
 
    for (int req : requests) { 
        if (req < head) left.push_back(req); 
        else right.push_back(req); 
    } 
 
    sort(left.begin(), left.end()); 
    sort(right.begin(), right.end()); 
 
    sequence.insert(sequence.end(), 
right.begin(), right.end()); 
    sequence.insert(sequence.end(), 
left.begin(), left.end()); 
 
    calculateSeekTime("CSCAN", sequence, 
head); 
} 
 
// LOOK Disk Scheduling 
void LOOK(vector<int> requests, int head, 
bool direction) { 
    vector<int> left, right; 
    vector<int> sequence; 
 
    for (int req : requests) { 
        if (req < head) left.push_back(req); 
        else right.push_back(req); 
    } 
 
    sort(left.begin(), left.end()); 
    sort(right.begin(), right.end()); 
 
    if (direction) { 
        sequence.insert(sequence.end(), 
right.begin(), right.end()); 
        sequence.insert(sequence.end(), 
left.begin(), left.rend()); 
    } else { 
        sequence.insert(sequence.end(), 
left.rbegin(), left.rend()); 
        sequence.insert(sequence.end(), 
right.begin(), right.end()); 
    } 
 
    calculateSeekTime("LOOK", sequence, 
head); 
} 
 
int main() { 
    vector<int> requests = {98, 183, 37, 122, 14, 
124, 65, 67}; 
    int head = 53; 
    int diskSize = 200; 
 
    FCFS(requests, head); 
    SSTF(requests, head); 
    SCAN(requests, head, diskSize, true);  // 
Moving towards the right (end of disk) 
    CSCAN(requests, head, diskSize); 
    LOOK(requests, head, true);  // Moving
    towards the right (end of disk) 
 
    return 0; 
} 






//files unix cp
 #include<iostream> 
#include<fstream> 
#include<string> 
 
using namespace std; 
 
// Function to simulate the UNIX 'cp' 
command: copy file content from source to 
destination 
void cp(const string& sourceFile, const string& 
destinationFile) { 
    ifstream src(sourceFile, ios::binary); 
    ofstream dest(destinationFile, ios::binary); 
 
    if (!src) { 
        cout << "Error: Source file '" << sourceFile 
<< "' cannot be opened.\n"; 
        return; 
    } 
 
    if (!dest) { 
        cout << "Error: Destination file '" << 
destinationFile << "' cannot be opened.\n"; 
        return; 
    } 
 
    dest << src.rdbuf();  // Copy the entire 
content of source file to destination file 
 
    cout << "File '" << sourceFile << "' copied to 
'" << destinationFile << "'.\n"; 
     
    src.close(); 
    dest.close(); 
} 
 
// Function to simulate the UNIX 'cat' 
command: display the contents of a file 
void cat(const string& filename) { 
    ifstream file(filename); 
     
    if (!file) { 
        cout << "Error: File '" << filename << "' 
cannot be opened.\n"; 
        return; 
    } 
 
    cout << "Displaying content of file: " << 
filename << "\n"; 
    string line; 
     
    while (getline(file, line)) { 
        cout << line << endl; 
    } 
 
    file.close(); 
} 
 
int main() { 
    string sourceFile, destinationFile, catFile; 
 
    // Simulate the 'cp' command 
    cout << "Enter the source file name for 'cp': 
"; 
    cin >> sourceFile; 
cout << "Enter the destination file name for 
'cp': "; 
cin >> destinationFile; 
cp(sourceFile, destinationFile); 
// Simulate the 'cat' command 
cout << "\nEnter the file name for 'cat': "; 
cin >> catFile; 
cat(catFile); 
return 0; 
}
