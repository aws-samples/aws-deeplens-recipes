## AWS DeepLens Recipes

This repo contains the source code for awsdeeplens.recipes, a website that has a curated collection of tutorials and classroom activities for [AWS DeepLens](https://aws.amazon.com/deeplens).

This repo also contains sample code for AWS DeepLens projects. You can find this code under /static/code

### Setup
The website is generated by the [Hugo](http://gohugo.io/) site generator.

#### Installing Hugo

#### On a Mac:
Install Hugo:
`brew install hugo`

#### On Linux:
  - Download from the releases page: https://github.com/gohugoio/hugo/releases/tag/v0.64.1
  - Extract and save the executable to `/usr/local/bin`

#### Clone this repo:
From wherever you checkout repos:
`git clone git@github.com:aws-samples/aws-deeplens-recipes.git` (or your fork)

#### Clone the theme submodule:
`cd aws-deeplens-recipes`

`git submodule init` ;
`git submodule update`

#### View website locally:
After installing Hugo, go to the project directory and enter:
`hugo serve`

Visit http://localhost:1313/ to see the site.
As you save edits to a page, the local site will live-reload to show your changes.

#### Auto Deploy:
Any commits to master will auto build and deploy in a couple of minutes. 

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

