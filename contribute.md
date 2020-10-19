# Contribute To This Guide

## This Guide Is Incomplete

There are many things wrong with this guide. There are spelling mistakes, grammar mistakes, ambiguous sentences, poor explanations. There are chapters which have yet to be written, there are exercises which have yet to be written, there are improvements to be made to the current exercises. The list goes on.

Fortunately, you are in a position to help improve this guide and make it better for everyone.

## This Guide Is Open Source

This is an open source guide. This means that the source code is freely available for anyone to view, download, and make changes to. You can find the source code on [GitHub](https://github.com/LukeBurgessYeo/fsharp-tutorial). The website itself is hosted by [GitHub Pages](https://pages.github.com/) and published directly from the previously mentioned GitHub repository, so changes made to that repository are immediately reflected on the website which you are reading right now.

As this guide is open source, that means you can help improve it. Before we get into how exactly to do that, we need to quickly mention impostor syndrome.

### Impostor Syndrome Disclaimer

[Wikipedia](https://en.wikipedia.org/wiki/Impostor_syndrome) describes impostor syndrome as:

> Impostor syndrome ... is a psychological pattern in which one doubts one's accomplishments and has a persistent internalised fear of being exposed as a "fraud".

This is extremely common amongst programmers (and other technical individuals), and often stops people from making valuable contributions to projects. In the case of this guide, your contributions are not only welcomed but encouraged.

No seriously, if you think something could be improved - go to the repository and do something about it. No issue is too small.

## How You Can Help

There are many, equally valuable, ways in which you can contribute to improving this guide. From asking for clarification of a particular concept which wasn't explained all that well, to correcting typos, or even writing whole new chapters. All contributions are important. Let's look at how exactly to make contributions.

For all contributions you will need a [GitHub account](https://github.com/join).

### Submitting Issues

If something is wrong with the site, i.e. it's not loading... (then how are you reading this?), or the links aren't working, etc. You can open an "issue" by going to [the repository](https://github.com/LukeBurgessYeo/fsharp-tutorial) and clicking on the "issues" tab. From there you can provide information about the issue. Someone will then look at your issue, maybe ask you some more questions about it, and hopefully fix it.

However, if the issue is something small, you may want to try fixing it yourself...

### Making Corrections

Perhaps you noticed a typo, or a link that doesn't work. These little fixes can be easily made without having to leave the comfort of your browser. Simply look through the source code (most of which is written in [markdown](https://guides.github.com/features/mastering-markdown/), which is extremely easy to read), find the file with the error and click the "edit" icon (a small pen) in the header just above the file.

From there you can make your edit and submit it to be reviewed. Once someone has reviewed your suggested change, it will hopefully be accepted and then automatically published to the site. You will then be able to claim the title of "open source contributor"!

But why stop there..?

### Suggesting Larger Changes

You may wish to make a larger change to the site, perhaps write a new chapter, rework an existing chapter, or make a structural change to the site. These are all great things that are fully encouraged, however, to improve the chances of these kinds of changes being accepted it is best to open an issue first to explain the change and start a conversation around how this should be done.

See below for details on how to run the site locally to test out these sorts of changes.

## Running This Site Locally

To get this site working on your local machine:
1. Install [git](https://git-scm.com/)
2. Install [node](https://nodejs.org/en/) (specifically for Node Package Manager - "npm")
3. Open a Terminal and clone the repository:

```bash
$ git clone https://github.com/LukeBurgessYeo/fsharp-tutorial.git
```

4. Install the required npm packages:

```bash
$ cd fsharp-tutorial
$ npm install
```

5. Start a local development server which will host your site and automatically load any changes you make to the source files into the site:

```bash
$ npm run start
```

6. Open a browser and view the site running at `http://127.0.0.1:4000` (by default).
7. Open the source files in a text editor and make some changes, then watch them automatically appear in the browser!
8. Submit a [pull request](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request)?

<note>

If any of the above steps fail, try searching online for a solution. Common problems include not having `git` or `npm` in your `PATH`. If you are still having problems, feel free to submit an issue!

</note>