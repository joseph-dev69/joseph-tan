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

