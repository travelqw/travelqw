Directory Management System 
#include <iostream>
#include <string>
#include <filesystem>
using namespace std;

namespace fs = std::filesystem;

// Class to handle directory information (not utilized in this version)
class Directory {
public:
    long phoneno;
    string fname, lname;

    Directory(long phoneno = 0, string fname = "", string lname = "")
        : phoneno(phoneno), fname(fname), lname(lname) {}
};

// Utility to print file information
void printFile(const fs::directory_entry& entry) {
    cout << entry.path().filename().string() << endl;
}

// List all files in the directory
void listAllFiles(const fs::path& path) {
    cout << "List of all Files:\n";
    for (const auto& entry : fs::directory_iterator(path)) {
        if (fs::is_regular_file(entry)) {
            printFile(entry);
        }
    }
}

// List files with a specific extension
void listFilesByExtension(const fs::path& path, const string& extension) {
    if (extension.empty() || extension[0] != '.') {
        cout << "Invalid extension format. It should start with a dot.\n";
        return;
    }

    cout << "Listing Files with Extension: " << extension << endl;
    bool found = false;
    for (const auto& entry : fs::directory_iterator(path)) {
        if (fs::is_regular_file(entry) && entry.path().extension() == extension) {
            printFile(entry);
            found = true;
        }
    }
    if (!found) {
        cout << "No files found with extension " << extension << endl;
    }
}

// List files matching a pattern
void listFilesByPattern(const fs::path& path, const string& pattern) {
    if (pattern.empty()) {
        cout << "Pattern should not be empty.\n";
        return;
    }

    cout << "Listing Files Matching Pattern: " << pattern << endl;
    bool found = false;
    for (const auto& entry : fs::directory_iterator(path)) {
        if (fs::is_regular_file(entry) && entry.path().filename().string().find(pattern) != string::npos) {
            printFile(entry);
            found = true;
        }
    }
    if (!found) {
        cout << "No files found matching pattern " << pattern << endl;
    }
}

// Create a new directory
void createDirectory(const fs::path& dirPath) {
    if (fs::exists(dirPath)) {
        cout << "Directory already exists: " << dirPath << endl;
    } else {
        if (fs::create_directory(dirPath)) {
            cout << "Directory Successfully Created: " << dirPath << endl;
        } else {
            cout << "Failed to create directory: " << dirPath << endl;
        }
    }
}

// Change the current working directory
void changeDirectory(fs::path& currentPath) {
    int choice;
    cout << "Change Directory Options:\n";
    cout << "1. Go to parent directory.\n";
    cout << "2. Go to root directory.\n";
    cout << "3. Specify a new directory.\n";
    cout << "Enter your choice: ";
    cin >> choice;

    switch (choice) {
        case 1:
            currentPath = currentPath.parent_path();
            break;
        case 2:
            currentPath = fs::path("/");
            break;
        case 3: {
            fs::path newPath;
            cout << "Enter the path of the directory: ";
            cin >> newPath;
            if (fs::exists(newPath) && fs::is_directory(newPath)) {
                currentPath = newPath;
            } else {
                cout << "Invalid path or directory does not exist." << endl;
            }
            break;
        }
        default:
            cout << "Invalid option!" << endl;
            return;
    }

    if (fs::exists(currentPath) && fs::is_directory(currentPath)) {
        fs::current_path(currentPath);
        cout << "Current directory changed to: " << fs::current_path() << endl;
    } else {
        cout << "Failed to change directory. Path is not valid or does not exist." << endl;
    }
}

int main() {
    fs::path currentPath = fs::current_path();
    int mainChoice;

    cout << "\nWelcome to Phone Directory and File Management System\n";
    do {
        cout << "\nMain Menu\n";
        cout << "_____________\n";
        cout << "1. Display List of Files\n";
        cout << "2. Create a New Directory\n";
        cout << "3. Change the Working Directory\n";
        cout << "4. Exit\n";
        cout << "Enter The Number: ";
        cin >> mainChoice;

        switch (mainChoice) {
            case 1: {
                int subChoice;
                cout << "\nList Files Detail\n";
                cout << "_______________\n";
                cout << "1. List all files.\n";
                cout << "2. List files by extension.\n";
                cout << "3. List files by pattern.\n";
                cout << "Enter your choice: ";
                cin >> subChoice;

                switch (subChoice) {
                    case 1:
                        listAllFiles(currentPath);
                        break;
                    case 2: {
                        string extension;
                        cout << "Enter the file extension (e.g., .txt): ";
                        cin >> extension;
                        listFilesByExtension(currentPath, extension);
                        break;
                    }
                    case 3: {
                        string pattern;
                        cout << "Enter the file name pattern (e.g., moha*.*): ";
                        cin >> pattern;
                        listFilesByPattern(currentPath, pattern);
                        break;
                    }
                    default:
                        cout << "Invalid option!" << endl;
                }
                break;
            }
            case 2: {
                string dirName;
                cout << "Enter the name of the directory: ";
                cin >> dirName;
                createDirectory(currentPath / dirName);
                break;
            }
            case 3:
                changeDirectory(currentPath);
                break;
            case 4:
                cout << "Thank you for using the system. Exiting...\n";
                break;
            default:
                cout << "Invalid option! Please try again.\n";
        }
    } while (mainChoice != 4);

    return 0;
}
