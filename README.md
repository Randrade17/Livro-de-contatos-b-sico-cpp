#  Livro de contato básico cpp

Um projeto simples em C++ para gerenciar uma agenda de contatos.

##  Funcionalidades
- Adicionar contatos
- Listar todos os contatos
- Buscar contatos por nome

##  Tecnologias
- C++11+
- STL (vector, string)

---

##  **Código completo `main.cpp`**

#include <iostream>
#include <vector>
#include <string>
#include <fstream>
#include <algorithm>

struct Contact {
    std::string name;
    std::string phone;
    std::string email;
};

// Nome do arquivo onde serão salvos os contatos
const std::string FILE_NAME = "contacts.txt";

void saveContactsToFile(const std::vector<Contact>& contacts) {
    std::ofstream outFile(FILE_NAME);
    if (!outFile) {
        std::cout << "Error saving file.\n";
        return;
    }
    for (const auto& c : contacts) {
        outFile << c.name << ";" << c.phone << ";" << c.email << "\n";
    }
    outFile.close();
}

void loadContactsFromFile(std::vector<Contact>& contacts) {
    std::ifstream inFile(FILE_NAME);
    if (!inFile) {
        // Arquivo ainda não existe
        return;
    }
    std::string line;
    while (std::getline(inFile, line)) {
        Contact c;
        size_t pos1 = line.find(';');
        size_t pos2 = line.rfind(';');
        if (pos1 != std::string::npos && pos2 != std::string::npos && pos1 != pos2) {
            c.name = line.substr(0, pos1);
            c.phone = line.substr(pos1 + 1, pos2 - pos1 - 1);
            c.email = line.substr(pos2 + 1);
            contacts.push_back(c);
        }
    }
    inFile.close();
}

void addContact(std::vector<Contact>& contacts) {
    Contact c;
    std::cout << "Enter name: ";
    std::getline(std::cin, c.name);
    std::cout << "Enter phone: ";
    std::getline(std::cin, c.phone);
    std::cout << "Enter email: ";
    std::getline(std::cin, c.email);
    contacts.push_back(c);
    std::cout << "Contact added successfully!\n";
    saveContactsToFile(contacts);
}

void listContacts(const std::vector<Contact>& contacts) {
    if (contacts.empty()) {
        std::cout << "No contacts to display.\n";
        return;
    }
    std::cout << "Contacts:\n";
    for (const auto& c : contacts) {
        std::cout << "Name: " << c.name
                  << ", Phone: " << c.phone
                  << ", Email: " << c.email << "\n";
    }
}

void searchContact(const std::vector<Contact>& contacts) {
    std::cout << "Enter name to search: ";
    std::string query;
    std::getline(std::cin, query);
    bool found = false;
    for (const auto& c : contacts) {
        if (c.name == query) {
            std::cout << "Found: "
                      << "Phone: " << c.phone
                      << ", Email: " << c.email << "\n";
            found = true;
        }
    }
    if (!found) {
        std::cout << "Contact not found.\n";
    }
}

void sortContacts(std::vector<Contact>& contacts) {
    std::sort(contacts.begin(), contacts.end(),
              [](const Contact& a, const Contact& b) {
                  return a.name < b.name;
              });
    std::cout << "Contacts sorted alphabetically.\n";
    saveContactsToFile(contacts);
}

void removeContact(std::vector<Contact>& contacts) {
    std::cout << "Enter name to remove: ";
    std::string query;
    std::getline(std::cin, query);
    auto it = std::remove_if(contacts.begin(), contacts.end(),
                             [&](const Contact& c) {
                                 return c.name == query;
                             });
    if (it != contacts.end()) {
        contacts.erase(it, contacts.end());
        std::cout << "Contact removed successfully.\n";
        saveContactsToFile(contacts);
    } else {
        std::cout << "Contact not found.\n";
    }
}

int main() {
    std::vector<Contact> contacts;
    loadContactsFromFile(contacts);

    int choice;

    do {
        std::cout << "\nContact Book Menu\n";
        std::cout << "1. Add Contact\n";
        std::cout << "2. List Contacts\n";
        std::cout << "3. Search Contact\n";
        std::cout << "4. Sort Contacts Alphabetically\n";
        std::cout << "5. Remove Contact\n";
        std::cout << "6. Exit\n";
        std::cout << "Enter choice: ";
        std::cin >> choice;
        std::cin.ignore(); // Para consumir a quebra de linha pendente

        switch (choice) {
            case 1:
                addContact(contacts);
                break;
            case 2:
                listContacts(contacts);
                break;
            case 3:
                searchContact(contacts);
                break;
            case 4:
                sortContacts(contacts);
                break;
            case 5:
                removeContact(contacts);
                break;
            case 6:
                std::cout << "Exiting program.\n";
                break;
            default:
                std::cout << "Invalid option.\n";
        }
    } while (choice != 6);

    return 0;
}
