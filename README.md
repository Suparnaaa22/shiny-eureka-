#include <iostream>
#include <vector>
#include <fstream>
#include <string>

struct Question {
    std::string text;
    std::vector<std::string> options;
    int correctOption;
};

std::vector<Question> questions;

void loadQuizzes(const std::string &filename) {
    std::ifstream file(filename);
    if (file) {
        Question question;
        while (std::getline(file, question.text)) {
            int numOptions;
            file >> numOptions;
            file.ignore(); // Ignore the newline character
            question.options.resize(numOptions);
            for (int i = 0; i < numOptions; ++i) {
                std::getline(file, question.options[i]);
            }
            file >> question.correctOption;
            file.ignore(); // Ignore the newline character
            questions.push_back(question);
        }
    }
}

void saveQuizzes(const std::string &filename) {
    std::ofstream file(filename);
    for (const auto &question : questions) {
        file << question.text << std::endl;
        file << question.options.size() << std::endl;
        for (const auto &option : question.options) {
            file << option << std::endl;
        }
        file << question.correctOption << std::endl;
    }
}

void takeQuiz() {
    int score = 0;
    for (const auto &question : questions) {
        std::cout << question.text << std::endl;
        for (size_t i = 0; i < question.options.size(); ++i) {
            std::cout << i + 1 << ". " << question.options[i] << std::endl;
        }
        int answer;
        std::cout << "Your answer (number): ";
        std::cin >> answer;
        if (answer - 1 == question.correctOption) {
            score++;
        }
    }
    std::cout << "Your score: " << score << "/" << questions.size() << std::endl;
}

void addQuestion() {
    Question question;
    std::cout << "Enter question text: ";
    std::cin.ignore(); // Ignore newline from previous input
    std::getline(std::cin, question.text);
    
    int numOptions;
    std::cout << "Enter number of options: ";
    std::cin >> numOptions;
    question.options.resize(numOptions);
    
    for (int i = 0; i < numOptions; ++i) {
        std::cout << "Option " << i + 1 << ": ";
        std::cin.ignore(); // Ignore newline
        std::getline(std::cin, question.options[i]);
    }
    
    std::cout << "Enter the correct option number: ";
    std::cin >> question.correctOption;
    questions.push_back(question);
}

int main() {
    loadQuizzes("quizzes.txt");
    int choice;
    do {
        std::cout << "1. Add Question\n2. Take Quiz\n3. Save Quizzes\n4. Exit\n";
        std::cout << "Enter your choice: ";
        std::cin >> choice;

        switch (choice) {
            case 1: addQuestion(); break;
            case 2: takeQuiz(); break;
            case 3: saveQuizzes("quizzes.txt"); break;
            case 4: std::cout << "Exiting...\n"; break;
            default: std::cout << "Invalid choice. Please try again.\n"; break;
        }
    } while (choice != 4);

    return 0;
}
