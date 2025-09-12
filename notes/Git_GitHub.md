# UWB 342 – Git & GitHub BootUp

## Zoom
[Link](https://washington.zoom.us/j/95310948090)

## Why Use Git?

> “Coding without Git is like driving without a seatbelt.”  

As students, it’s easy to lose track of your work — copying folders like `hw1-final`, `hw1-final-FINAL`, or emailing files back and forth with teammates. Git solves this problem by giving you a **time machine** for your projects: you can save versions of your code (commits), experiment safely without breaking things (branches), and work with others without overwriting each other’s progress (merges and pull requests). Whether you’re taking notes, writing code alone, or collaborating in a team project, Git helps you stay organized and makes your work professional from day one.  

Beyond school, Git is the **industry standard**. Every major tech company — from startups to giants like Microsoft, Google, and Amazon — uses Git (often with GitHub, GitLab, or Bitbucket) to manage real-world projects with hundreds or thousands of developers. If you learn Git now, you’ll not only make your coursework easier, but you’ll also be preparing yourself with a core skill that employers expect. Mastering Git is like learning to drive — once you know it, you can go anywhere in the software world.

---

## 1. Introduction

Git is a **version control system (VCS)** — it helps you track changes to your files and collaborate with others.  
Think of it as a "time machine" for your code and documents. You can save snapshots (commits), go back in time, and work with others without overwriting each other’s work.  

GitHub is a **cloud platform** that hosts your Git repositories online. Git is the tool, GitHub is the service.  

<img width="40%" height="755" alt="image" src="https://github.com/user-attachments/assets/0f2d784e-5d9b-4056-843b-bf2449b9b855" />

| **Git** | **GitHub** |
|---------|------------|
| Local tool installed on your computer | Cloud service that hosts repositories |
| Tracks changes, manages commits & branches | Provides collaboration features like pull requests, reviews, and issues |
| Works offline | Requires internet to sync |
| Examples: `git init`, `git commit`, `git branch` | Examples: `git push`, `git pull`, opening PRs |

---

## 2. Quick Introduction to Git

### Why use Git?
- Save progress without copying folders like `project_v1`, `project_v2`.
- Experiment safely with branches.
- Collaborate with teammates without emailing files back and forth.
- Contribute to open-source projects.

### Key Concepts
- **Repository (repo):** A folder tracked by Git.
- **Commit:** A snapshot of your files at a point in time.
- **Branch:** A parallel line of development.
- **Merge/Rebase:** Combine work from different branches.
- **Remote:** A repo stored on GitHub (or elsewhere).

---

## 4. Flow 1: Using Git for Notes (Markdown)

Sometimes, Git is useful not just for code, but also for **taking notes**.

### Steps
1. Create a new repository on GitHub (example: `my-notes`).
2. Clone it locally:
   ```bash
   git clone https://github.com/username/my-notes.git
   cd my-notes
   ```
3. Create a Markdown file:
   ```bash
   echo "# My Notes" > notes.md
   git add notes.md
   git commit -m "Add notes.md"
   git push
   ```

### Markdown Basics

```markdown
# Section Title
## Subsection
- Bullet list
1. Numbered list
**Bold** and *italic*
```

👉 This way, you can use Git + Markdown to build a **version-controlled notebook**.

---

## 5. Flow 2: Using Git for Your Own Code

Now let’s use Git for a small project.

### Example: Extract Digits Vertically

We’ll write a function that, given a positive integer, extracts each digit and stores it in a vector.  
Example: `123` → `[1, 2, 3]`.

```cpp
#include <iostream>
#include <vector>

// Recursively extract digits of n and store in result
void extractDigits(int n, std::vector<int>& result) {
    if (n == 0) return;
    extractDigits(n / 10, result); // recursion goes left to right
    result.push_back(n % 10);      // take last digit
}

// Define test case structure
struct TestCase {
    int input;
    std::vector<int> expected;
};

// Function to run a single test
void testExtractDigits(const TestCase& t) {
    std::vector<int> result;
    extractDigits(t.input, result);

    if (result == t.expected) {
        std::cout << "PASS: input=" << t.input << std::endl;
    } else {
        std::cout << "FAIL: input=" << t.input << std::endl;
        std::cout << "Expected: ";
        for (int d : t.expected) std::cout << d << " ";
        std::cout << "\nGot: ";
        for (int d : result) std::cout << d << " ";
        std::cout << "\n";
    }
}

int main() {
    std::vector<TestCase> tests = {
        {123, {1, 2, 3}},
        {405, {4, 0, 5}},
        {7,   {7}},
        {1002, {1, 0, 0, 2}}
    };

    for (size_t i = 0; i < tests.size(); i++) {
        testExtractDigits(tests[i]);
    }
    return 0;
}

```

### Git Workflow
1. Create a repo `digit-extractor`.
2. Commit the initial project skeleton.
3. Add tests → commit.
4. Add implementation → commit.
5. Push to GitHub.

👉 Each feature or bugfix can be done in a **branch** before merging into `main`.

---

## 6. Flow 3: Collaborating with Team Members

### Scenario
Two developers working on the same repo:
- Each works in their own branch.
- When ready, they create a **Pull Request (PR)** on GitHub.
- The teammate reviews, possibly rebases, and merges.

### Steps
1. Clone repo and create branch:
   ```bash
   git checkout -b feature/new-function
   ```
2. Work on code, commit:
   ```bash
   git add file.cpp
   git commit -m "Add new function"
   ```
3. Push branch:
   ```bash
   git push origin feature/new-function
   ```
4. Open PR on GitHub.
5. Review, fix conflicts, merge into `main`.

👉 Teamwork is smoother with clear commit messages and branches per feature.

---

## 7. Summary

- Git is your **time machine** for code and notes.  
- GitHub is where collaboration happens.  
- Three practical flows:
  1. Notes with Markdown.  
  2. Personal project with commits & branches.  
  3. Team collaboration with PRs.  

Next step: practice! Create a repo today, even if it’s just for your notes.

---
