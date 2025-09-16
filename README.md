Explain a Git workflow example using these commands
Scenario: Developing a new feature for a web app
This example demonstrates a common team-based Git workflow using a feature-branch approach. Let's imagine you're working with a team on a website and need to add a "contact us" form. 
1. Preparation: Start with the latest code
Before you begin, ensure your local main branch is up-to-date with any changes your teammates have pushed to the remote repository. 
sh
# Switch to the main branch
git checkout main
# Pull the latest changes from the remote repository
git pull origin main
Use code with caution.

2. Create a feature branch
Create a new, descriptively named branch for your work to isolate your changes from the main codebase until they are ready. 
sh
# Create and switch to a new branch for the contact form
git checkout -b feature/contact-form
Use code with caution.

3. Develop the new feature
Now you can start coding. As you make progress, you'll commit your changes incrementally.
Create the contact form file:
sh
touch contact.html
echo "<h1>Contact Us</h1>" > contact.html
Use code with caution.

Stage and commit the new file:
sh
# Stage the new file for commit
git add contact.html
# Commit the changes with a clear, descriptive message
git commit -m "Add contact page with basic heading"
Use code with caution.

Add more functionality:
sh
# Make further changes to contact.html
echo "<form>...</form>" >> contact.html
Use code with caution.

Stage and commit the additional changes:
sh
# Stage the modified file
git add contact.html
# Commit the changes
git commit -m "Add form elements to contact page"
Use code with caution.

4. Push your branch to the remote
When you are ready to share your work or take a break, push your feature branch to the remote repository. This creates a backup and allows your teammates to see your progress. 
sh
# Push your branch and set it as the upstream branch
git push -u origin feature/contact-form
Use code with caution.

5. Open a pull request
Once you are confident your feature is complete and ready for review, you will open a pull request (PR) through your Git hosting service (e.g., GitHub, GitLab). In the PR, you'll: 
Write a descriptive title and message explaining your changes.
Request feedback from your teammates. 
6. Review and address feedback
While you wait for your team's review, you might need to update your branch with new changes from the main project. 
sh
# Switch to the main branch
git checkout main
# Pull any new changes
git pull origin main
# Switch back to your feature branch
git checkout feature/contact-form
# Merge the updated main branch into your feature branch
git merge main
Use code with caution.

If your teammates request changes, you'll make the updates, commit them, and push the changes to your feature branch. The pull request will update automatically with your new commits. 
7. Merge the pull request and clean up
After your PR is reviewed and approved, you can merge your feature branch into the main branch. This is typically done via the Git hosting service's user interface. After merging, it's good practice to delete the now-obsolete feature branch. 
sh
# Switch back to the main branch
git checkout main
# Delete the local feature branch
git branch -d feature/contact-form
