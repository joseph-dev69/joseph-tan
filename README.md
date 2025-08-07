#include <iostream>
#include <string>
#include <filesystem> // C++17
#include <direct.h>   // For _mkdir, _chdir, _getcwd

using namespace std;
namespace fs = std::filesystem;

void displayCurrentDirectory() {
    char buffer[FILENAME_MAX];
    _getcwd(buffer, FILENAME_MAX);
    cout << "\nCurrent Directory: " << buffer << endl;
}

void listAllFiles() {
    cout << "\nFiles in current directory:\n";
    for (const auto& entry : fs::directory_iterator(fs::current_path())) {
        if (fs::is_regular_file(entry))
            cout << "- " << entry.path().filename().string() << endl;
    }
}

void listFilesByExtension(const string& ext) {
    cout << "\nFiles with extension " << ext << ":\n";
    for (const auto& entry : fs::directory_iterator(fs::current_path())) {
        if (fs::is_regular_file(entry) && entry.path().extension() == ext)
            cout << "- " << entry.path().filename().string() << endl;
    }
}

void listFilesByPattern(const string& pattern) {
    cout << "\nFiles matching pattern \"" << pattern << "\":\n";
    for (const auto& entry : fs::directory_iterator(fs::current_path())) {
        if (fs::is_regular_file(entry)) {
            string filename = entry.path().filename().string();
            if (filename.find(pattern) != string::npos)
                cout << "- " << filename << endl;
        }
    }
}

void listFiles() {
    int choice;
    cout << "\n[1] List All Files\n[2] List by Extension\n[3] List by Pattern\nEnter choice: ";
    cin >> choice;
    cin.ignore();

    switch (choice) {
        case 1:
            listAllFiles();
            break;
        case 2: {
            string ext;
            cout << "Enter file extension (e.g., .txt): ";
            getline(cin, ext);
            listFilesByExtension(ext);
            break;
        }
        case 3: {
            string pattern;
            cout << "Enter file pattern (substring match, e.g., moha): ";
            getline(cin, pattern);
            listFilesByPattern(pattern);
            break;
        }
        default:
            cout << "Invalid choice.\n";
    }
}

void createDirectory() {
    string dirName;
    cout << "\nEnter directory name: ";
    cin.ignore();
    getline(cin, dirName);

    fs::path dirPath = fs::current_path() / dirName;
    if (fs::exists(dirPath)) {
        cout << "Directory \"" << dirName << "\" already exists!\n";
    } else {
        if (fs::create_directory(dirPath))
            cout << "Directory \"" << dirName << "\" created successfully.\n";
        else
            cout << "Error creating directory.\n";
    }
}

void changeDirectory() {
    int choice;
    cout << "\n[1] Move to Parent Directory\n[2] Move to Root Directory\n[3] Enter Custom Path\nEnter choice: ";
    cin >> choice;
    cin.ignore();

    fs::path current = fs::current_path();

    switch (choice) {
        case 1: {
            fs::path parent = current.parent_path();
            if (!parent.empty() && fs::exists(parent)) {
                fs::current_path(parent);
                cout << "Current directory changed to: " << parent << endl;
            } else {
                cout << "Error: Unable to move to parent directory.\n";
            }
            break;
        }
        case 2: {
            fs::path root = current.root_path();
            if (!root.empty() && fs::exists(root)) {
                fs::current_path(root);
                cout << "Current directory changed to: " << root << endl;
            } else {
                cout << "Error: Unable to move to root directory.\n";
            }
            break;
        }
        case 3: {
            string path;
            cout << "Enter path: ";
            getline(cin, path);
            if (fs::exists(path) && fs::is_directory(path)) {
                fs::current_path(path);
                cout << "Current directory changed to: " << fs::current_path() << endl;
            } else {
                cout << "Error: Directory \"" << path << "\" not found!\n";
            }
            break;
        }
        default:
            cout << "Invalid choice.\n";
    }
}

void mainMenu() {
    int choice;
    do {
        displayCurrentDirectory();
        cout << "\n--- Directory Management System ---\n";
        cout << "[1] List Files\n[2] Create Directory\n[3] Change Directory\n[4] Exit\n";
        cout << "Enter choice: ";
        cin >> choice;

        switch (choice) {
            case 1: listFiles(); break;
            case 2: createDirectory(); break;
            case 3: changeDirectory(); break;
            case 4: cout << "Exiting program...\n"; break;
            default: cout << "Invalid choice.\n";
        }
    } while (choice != 4);
}

int main() {
    mainMenu();
    return 0;
}

