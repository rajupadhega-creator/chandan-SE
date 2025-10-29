# chandan-SE
library management system
#include <iostream>
#include <fstream>
#include <string>
#include <vector>
#include <map>
#include <algorithm>
using namespace std;

// ---------- Candidate Class ----------
class Candidate {
public:
    string name;
    int votes;

    Candidate(string n = "") {
        name = n;
        votes = 0;
    }
};

// ---------- Voter Class ----------
class Voter {
public:
    string voterID;
    string password;
    bool hasVoted;

    Voter(string id = "", string pwd = "") {
        voterID = id;
        password = pwd;
        hasVoted = false;
    }
};

// ---------- Voting System Class ----------
class VotingSystem {
private:
    vector<Voter> voters;
    vector<Candidate> candidates;
    bool votingOpen;

public:
    VotingSystem() {
        votingOpen = false;
    }

    // Load Candidates (could be from a file in real systems)
    void addCandidate(string name) {
        candidates.push_back(Candidate(name));
    }

    // Register a new voter
    void registerVoter(string id, string pwd) {
        voters.push_back(Voter(id, pwd));
        cout << "Voter registered successfully!\n";
    }

    // Start and close election
    void startVoting() {
        votingOpen = true;
        cout << "\nVoting has started.\n";
    }

    void closeVoting() {
        votingOpen = false;
        cout << "\nVoting has been closed.\n";
    }

    // Authenticate voter
    Voter* authenticate(string id, string pwd) {
        for (auto &v : voters) {
            if (v.voterID == id && v.password == pwd) {
                return &v;
            }
        }
        return nullptr;
    }

    // Display candidates
    void displayCandidates() {
        cout << "\n--- Candidates List ---\n";
        for (size_t i = 0; i < candidates.size(); i++) {
            cout << i + 1 << ". " << candidates[i].name << endl;
        }
    }

    // Cast a vote
    void castVote(string id, string pwd) {
        if (!votingOpen) {
            cout << "Voting is not open yet!\n";
            return;
        }

        Voter* voter = authenticate(id, pwd);
        if (!voter) {
            cout << "Invalid credentials!\n";
            return;
        }
        if (voter->hasVoted) {
            cout << "You have already voted!\n";
            return;
        }

        displayCandidates();
        int choice;
        cout << "Enter candidate number: ";
        cin >> choice;

        if (choice < 1 || choice > (int)candidates.size()) {
            cout << "Invalid choice!\n";
            return;
        }

        candidates[choice - 1].votes++;
        voter->hasVoted = true;
        cout << "Vote cast successfully for " << candidates[choice - 1].name << "!\n";
    }

    // Show results
    void showResults() {
        if (votingOpen) {
            cout << "Please close the voting before viewing results!\n";
            return;
        }

        cout << "\n--- Election Results ---\n";
        int maxVotes = -1;
        string winner;

        for (auto &c : candidates) {
            cout << c.name << " -> " << c.votes << " votes\n";
            if (c.votes > maxVotes) {
                maxVotes = c.votes;
                winner = c.name;
            }
        }

        cout << "\nWinner: " << winner << " 🎉\n";
    }

    // Save results to file
    void saveResultsToFile() {
        ofstream file("voting_results.txt");
        for (auto &c : candidates) {
            file << c.name << " " << c.votes << endl;
        }
        file.close();
        cout << "\nResults saved to 'voting_results.txt'\n";
    }
};

// ---------- Main Function ----------
int main() {
    VotingSystem vs;
    int choice;

    // Preload candidates
    vs.addCandidate("Alice");
    vs.addCandidate("Bob");
    vs.addCandidate("Charlie");

    // Pre-register voters
    vs.registerVoter("v1", "1234");
    vs.registerVoter("v2", "5678");
    vs.registerVoter("v3", "0000");

    do {
        cout << "\n===== ONLINE VOTING SYSTEM =====\n";
        cout << "1. Start Voting\n";
        cout << "2. Cast Vote\n";
        cout << "3. Close Voting\n";
        cout << "4. Show Results\n";
        cout << "5. Save Results to File\n";
        cout << "6. Exit\n";
        cout << "Enter your choice: ";
        cin >> choice;

        string id, pwd;
        switch (choice) {
            case 1:
                vs.startVoting();
                break;
            case 2:
                cout << "Enter Voter ID: ";
                cin >> id;
                cout << "Enter Password: ";
                cin >> pwd;
                vs.castVote(id, pwd);
                break;
            case 3:
                vs.closeVoting();
                break;
            case 4:
                vs.showResults();
                break;
            case 5:
                vs.saveResultsToFile();
                break;
            case 6:
                cout << "Exiting system...\n";
                break;
            default:
                cout << "Invalid choice!\n";
        }

    } while (choice != 6);

    return 0;
}


