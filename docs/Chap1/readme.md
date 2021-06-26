## Docsify Quick tourial <!-- {docsify-ignore} -->

> An awesome Docs-site generator.


### Basic Structure
- index.html: There are two functions: one is to act as the portal to the site, that is, the load page that appears when the site is opened at the beginning, and the other is to  
 as a profile
- README.md: That is, the site root page, that is, the common concept of the site's index .html, for the home page content rendering  
- .nojekyll: Used to prevent GitHub Pages from ignoring files that fall off the beginning of the underscore


### Multi-page Document
> the directory structure is as follows:    
```
  Docs
	├── index.html
	├── README.md       =>  http://domain.com
	└── FileDomain 
        ├── README.md   =>  http://domain.com/FileDomain 
	      └── test.md     =>  http://domain.com/FileDomain/test
```
 If you need more pages, you can simply create more markdown files in your docsify directory. If you create a file named <code>guide.md</code>, then it is accessible via <code>/#/guide</code>.

### Code Highlighting
Docsify uses Prism to highlight code blocks in your pages.
Support for languages is available by loading the language-specific grammar files via CDN:

```html
  1.<script src="//unpkg.com/prismjs/components/prism-bash.js"></script> 
  2.<script src="//unpkg.com/prismjs/components/prism-php.js"></script>
```
_The above two lines support bash and php syntax highlighting, and the other syntax is the same._

 
> It should be noted that CPP is special, syntax highlighting requires the addition of two additional files: prism-c and prism-clike, and then highlighting with other CPP syntaxes and then adding them separately, that is, any CPP syntax highlighting requires adding three lines, for example:
```html
  1.<script src="//unpkg.com/prismjs/components/prism-c.js"></script>
  2.<script src="//unpkg.com/prismjs/components/prism-clike.js"></script>
  3.<script src="//unpkg.com/prismjs/components/prism-cpp.js"></script> 
```
