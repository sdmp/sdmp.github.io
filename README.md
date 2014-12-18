# Secure Distributed Messaging Protocol (SDMP)

#### Current status: `alpha`

Read the complete specs at [sdmp.github.io/docs/](http://sdmp.github.io/docs/)
or [on GitHub](https://github.com/sdmp/docs).

## Github layout of docs and code

	sdmp.github.io 		the main intro page
		master
			just the main intro page

	docs 				all the main docs
		master
			at whatever "stable" is
		development
			development for next release
		gh-pages
			an exact copy of "master" branch

	$MODULE
		master
			at whatever "stable" is
		development
			development for next release
		gh-pages 		not all modules, just ones that make sense to have a page, like a CLI implementation of a spec
			either an exact copy of "master", or a custom made one, depending on what is needed
