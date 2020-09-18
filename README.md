This example is based heavily on the example discussed here: https://www.youtube.com/watch?v=m5D-yoH416Y

I have asked you to first watch that video. Go through her example before going through this example that is more specific to 481. 

For your later reference, if you want to learn more about bookdown, the official guide is https://bookdown.org/yihui/bookdown


Here are the steps to follow:

### 1. Copy this template

<ol type="a">
  <li>
Click the green "Use this template" button in GitHub. Give it your own name, something like BookdownExample. Please make it a Private repo instead of public.
  </li>
 </ol>
 
### 2. Set up GitHub Pages

<ol type="a">
  <li>
  On the home page of your repo, click Settings. Scroll down to the GitHub pages section and change **Source** to **master branch /docs folder**.  Above the **Source** line, a bar will appear with your book's URL. The bar will initially be blue and indicate that your book is *ready* to be published and will change to green once it is published. Copy the URL. (Note that sometimes there is a delay until your book actually appears at that URL. If it doesn't appear after a few minutes, make a change and commit it to trigger a GitHub Pages build.)
  </li>
  <li>
  Click the gear button near "About" on the home page of the repo and paste your book URL into the **Website** field that appears on the right.
  </li>
</ol>

### 3. Clone the repo to RStudio

<ol type="a">
  <li>
Clone your new repo with *File, New Project..., Version Control, Git* in RStudio, just like you did for [Ch 15 of Happy Git with R](https://happygitwithr.com/new-github-first.html)    
  </li>
 </ol>


### 4. Edit the few lines that are specific to you personally

<ol type="a">
  <li>
In the first example (the youtube video), you edited the book title and author in  index.Rmd`. For this book, you can leave the title and author as-is. For your main BP book, we're going to leave the author as anonymous instead of using your name. This is because while the repo is private, the GitHub Pages is not. While you're working on it for class, you don't necessarily want your name attached to it. So each student's book will not have their name attached to it (after the class is over, it's your book and you're welcome to change that, but during the term, we'll keep it anonymous.). But please do read the preface after you build the book (even though you don't have to change anything in `index.Rmd`). The preface explains how you'll always load data for 481 (and the two chapters provide examples).
  </li>
  <li>
In `_bookdown.yml`
  <ol type="i">
    <li>
    change YOUR GITHUB USERNAME to your GitHub username in the three places it appears
    </li>
    <li>
    change YOUR GITHUB REPO to your GitHub repo name in the three places it appears.
    </li>
    <li>
    These three links set links that appear in your book, the "edit" one taking you to the GitHub page to edit the RMD file, the "view" one taking you to GitHub to view the RMD file, and the "history" one taking you to the GitHub page to view the history of edits/commits for the file. Having these links make it a lot easier to quickly look at your files without always having to pull everythign to my own computer. So please make sure to set them. 
    </li>    
  </li>
 </ol>




### 5. Build the book
  <ol type="a">
    <li>
        Install **bookdown** with `install.packages("bookdown")` (from either the Console or from an RMD file code chunk, but don't leave it in the RMD file after you've installed it)
    </li>
    <li>
    Click on the "Build Book" button on the Build tab (Build tab is next to the Git tab)
    </li>
   </ol>

### 6. Commit and Push to GitHub

<ol type="a">
  <li>
    When you're happy with it, commit everything (it's a lot of files, so make sure to select them all) and push to GitHub
  </li>
  <li>
    Check in GitHub to make sure everything is updated there. Click on that link from the second step to see the contents of your book on GitHub. 
  </li>
 </ol>


### 7. Try changing an RMD file in RStudio

<ol type="a">
  <li>
    Back in RStudio, make a small change to one of the RMD files. 
  </li>
  <li>
    Knit that one RMD file. The book should re-open with the changes you made
  </li>
  <li>
    Commit the changes and push them to GitHub
  </li>
  <li>
    Make sure you see the changes online in GitHub Pages
  </li>
  <li>
    This is how you will work on and update your BP book. When you want me to check it out, you'll @ me in your Commit message like you tried out in the first assignment. You'll make many commits while you work. I don't need to see them all. I'll just check it out when you want me to (which you signal by tagging me). 
  </li>
  <li>
    Note that sometimes it takes 5 or so minutes for changes you push to GitHub to show up on GitHub Pages.
  </li>
</ol>

### 8. Share your repo with me (jrlhost)

I will check out your repo with your book to make sure it includes both chapters and shows a commit history based on step 7. 

edit by gabe to be pushed

