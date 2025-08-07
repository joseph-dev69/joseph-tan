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

