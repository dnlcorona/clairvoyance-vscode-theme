
<template>
  <header>
    <img
      alt="Vue logo"
      class="logo"
      src="@/assets/logo.svg"
      width="125"
      height="125"
    />

    <div class="wrapper">
      <HelloWorld msg="You did it!" />

      <nav>
        <RouterLink to="/">Home</RouterLink>
        <RouterLink to="/about">About</RouterLink>
      </nav>
    </div>
  </header>

  <RouterView />
</template>

<template lang="pug">
	.fmly-row
		.fmly(v-if='datos', v-for='family in datos')
			.couple
				.ref(:class="parentsFlag(family.couple)")
				.person(v-for='(person,index) in family.couple', :key='person+index', :class="'a' + person")
					.person-item
						.person-item__img(:style="{'background-image': 'url('+personas[person].img+')'}")
							router-link.person-item__edit(:to="{name: 'edit' , params: { id: person }}")
						.person-item__info
							.person-item__info__name
								span {{personas[person].name}}
							.person-item__info__others
								span.person-item__info__nickname {{personas[person].nickname}}
								span.person-item__info__year(v-if="personas[person].dates.birth") {{dateTransform(personas[person].dates.birth.seconds)}}
						.person-item__action


			- var friends = 10
					case friends
						when 0
							p you have no friends
						when 1
							p you have a friend
						default
							p you have #{friends} friends

			- for (var x = 0; x < 3; x++)
				li item
						


			-
				var list = ["Uno", "Dos", "Tres", "Cuatro", "Cinco", "Seis"]
				
				each item in list
					li= item

			ul
				each val, index in ['zero', 'one', 'two']
					li= index + ': ' + val


			- var user = { description: 'foo bar baz' }
			- var authorised = false
			#user
				if user.description
					h2.green Description
					p.description= user.description
				else if authorised
					h2.blue Description
					p.description.
						User has no description,
						why not add one...
				else
					h2.red Description
					p.description User has no description
					

			//- index.pug
			doctype html
			html
				include includes/head.pug
				body
					h1 My Site
					p Welcome to my super lame site.
					include includes/foot.pug


			//- layout.pug
			html
				head
					title My Site - #{title}
					block scripts
						script(src='/jquery.js')
				body
					block content
					block foot
						#footer
							p some footer content

			mixin list
				ul
					li foo
					li bar
					li baz
			//- Use
			+list




			<!--[if gt IE 8]><!-->
			//- 				router-link(:to="{name:'add'}").person-item__add
			// vfamilynode(v-if='family.relatives', :datos='family.relatives', :personas="personas")
</template>

<script>
export default {	
	name: "vfamilynode",
	props: ["datos","personas"],
	data() {
		return {
		};
	},
	watch: {},
	methods: {
		parentsFlag(couple){
			let classNames = ""
			if(couple.length > 1){
				let cl1 = "a"
				let cl2 = "a"
				for (let i = 0; i < couple.length; i++){
					cl1 = cl1 + couple[i]
				}
				for (let i = couple.length - 1; i >= 0; i--){
					cl2 = cl2 + couple[i]
				}
				return classNames = cl1 + " " + cl2
			}else{
				return null
			}
		},
		dateTransform(timestamp){
			var a = new Date(0);
			a.setUTCSeconds(timestamp);
			var months = ['Ene','Feb','Mar','Abr','May','Jun','Jul','Ago','Sep','Oct','Nov','Dic'];
			var year = a.getUTCFullYear();
			var month = months[a.getUTCMonth()];
			var date = a.getUTCDate();
			var time = date + ' ' + month + ' ' + year ;
			return time;
		},
		makeLines(){
			const app = this
			const svg_id = 'linea-1'
			// cual es el row <
			const lowestRow = app.personas[Object.keys(app.personas)[0]].row
			// console.log(lowestRow)
			// este es el current Row
			let currentPerson = app.datos[Object.keys(app.datos)[0]].couple[0]
			let currentRow = app.personas[currentPerson].row
			// console.log(currentRow)
			// esto es porque el ultimo componente en terminar es el primero
			if(currentRow == lowestRow){
				// SCROLLING LEFT START
				const fmly_wrapper = document.querySelector("#app")
				let fmly_wrapper_bcr = fmly_wrapper.getBoundingClientRect()
				const $fmly_row = document.querySelector('.fmly-row')
				let $family_row_bcr = $fmly_row.getBoundingClientRect()
				
				fmly_wrapper.scrollLeft += ($family_row_bcr.width / 2) - (fmly_wrapper_bcr.width / 2)
				// SCROLLING LEFT END
				// old line
				let $oldLine = document.getElementById(svg_id)
				// if there's a old line remove
				if ($oldLine) $oldLine.remove()
				// provitional cords
				let cords = {}
				// line object
				const $line = document.getElementById('line')
				// console.log($line)
				let svg_width =	$line.offsetWidth
				let svg_height = $line.offsetHeight
				// console.log(svg_width + '-' + svg_height)
				// creacion de svg
				let $svg = fnCreateSvg($line,svg_id,svg_width,svg_height)
				// loop in $store.lines
				for (let key in app.lines){
					
					// if $store.lines[key] has data
					if(app.lines[key]){
						// console.log(app.personas[key].name)
						
						const $lineCords = $line.getBoundingClientRect()
						// person object and cords
						const $person = document.querySelector("." + 'a' + key)
						const $personCords = $person.getBoundingClientRect()
						// console.log($personCords)
						// parent of person object and cords
						const $parent = document.querySelector("." + app.lines[key])
						const $parentCords = $parent.getBoundingClientRect()
						// console.log($parentCords)
						// create line between person and parent
						createPolyLine($svg, $parentCords.x - $lineCords.x + ($parentCords.width /2), $parentCords.bottom - $lineCords.y - 10, $personCords.x - $lineCords.x + ($personCords.width /2), $personCords.y - $lineCords.y + 10)
						// console.log("--------------------");
					}
					
				}
				
			}
			// CREAR SVG EN EL DOM
			function fnCreateSvg(csctnr,csid,cswidth,csheight){
				var NS = "http://www.w3.org/2000/svg"
				var svg = document.createElementNS(NS, "svg")
				svg.setAttribute("id",csid)
				svg.setAttribute("xmlns:xlink","http://www.w3.org/1999/xlink")
				svg.setAttribute("viewBox", "0 0 " + cswidth + " " + csheight)
				
				csctnr.appendChild(svg)
				return svg
			}
			// CREAR LINEA DENTRO DEL SVG
			function createLine(clctnr,clx1,cly1,clx2,cly2){
				var NS = "http://www.w3.org/2000/svg"
				var line = document.createElementNS(NS, "line")
				line.setAttribute("x1",clx1)
				line.setAttribute("y1",cly1)
				line.setAttribute("x2",clx2)
				line.setAttribute("y2",cly2)
				
				clctnr.appendChild(line)
			}
			// CREAR POLYLINEA DENTRO DEL SVG
			function createPolyLine(clctnr,clx1,cly1,clx2,cly2){
				var NS = "http://www.w3.org/2000/svg"
				var line = document.createElementNS(NS, "polyline")
				var hWay =	cly2 - 35 
				var cords = clx1 + "," + cly1 + " " + clx1 + "," + hWay + " " + clx2 + "," + hWay + " " + clx2 + "," + cly2
				line.setAttribute("points",cords)
				
				clctnr.appendChild(line)
			}
			// console.log("triguer________")
		}
	},
	computed:{
		lines(){
			return this.$store.state.lines
		}
	},
	created() {},
	mounted() {
		this.$nextTick(function () {
			this.makeLines()
		})
	},
	updated() {
		this.makeLines()
	}
}
</script>

<style lang="sass">
@function my-calculation-function($some-number, $another-number)
	@return $some-number + $another-number


@mixin my-padding-mixin($some-number)
	padding: $some-number


a
	font-family: $link-font-family
	font-size: $link-font-size
	font-weight: $link-font-weight
	text-decoration: none
	background-color: transparent
	-webkit-text-decoration-skip: objects

		@include my-padding-mixin(10px)
		+size(300px)

	&:hover

	&:active, &:hover
		outline-width: 0

	&:active, &:focus, &:hover
		color: $link-hover-color

#{$all-buttons}
	appearance: none
	cursor: pointer
	display: inline-block
	border-radius: $base-border-radius
	color: $white
	background-color: $link-color
	font-family: $link-font-family
	transition: background-color $base-duration $base-timing


button::-moz-focus-inner, [type="button"]::-moz-focus-inner, [type="reset"]::-moz-focus-inner, [type="submit"]::-moz-focus-inner
	border-style: none
	padding: 0

button:-moz-focusring, [type="button"]:-moz-focusring, [type="reset"]:-moz-focusring, [type="submit"]:-moz-focusring
	outline: 1px dotted ButtonText


div
	color: #00ff00

	// nested rules
	#inner.element
		color: #000000


	// parent selector
	&:hover
		text-decoration: underline

	body.firefox &
		font-weight: normal

	&__element, // compound selector
	&--modifier
		border: 1px


	// nested properties
	font:
		family: fantasy
		size: 30em
		weight: bold

	font: 20px/24px fantasy
		weight: bold




/*
 * variables, data types
 */

// assignment
$number:								5em
$double-quoted-string:	"foo"
$single-quoted-string:	'bar'
$not-quoted-string:			baz
$true:									true
$false:									false
$null:									null
$parens-spaces-list:		(left top)
$parens-commas-list:		(1px, 2px, 3px, 4px)
$no-parens-spaces-list:	1px 2px 3px 4px
$no-parens-commas-list:	arial, some-other-arial, sans-serif
$trailing-comma:				(1 2 3,) // a comma-separated list containing a space-separated list
$bracketed-list:				[5rem 6rem 7rem] // comma or space separated
$map:										(medium: 640, 'large': 960, "x-large": 1280, (xx-large,): 1600) // must have parentheses, be comma separated
$color:									#fe57a1
$function-reference:		get-function($function-name)

// assign if not already assigned to
$var: 1 !default

#main
	// global variable defined in block
	$width: 5em !global

	// list of parent selectors
	$selectors: &

	// property value
	width: $width


// media feature test
@import "foo" ($orientation: $landscape)
@media ($orientation-landscape)
		width: 700px


// font feature value
@font-feature-values Font One
		@styleset
				nice-style: $value
		


// @supports test
@supports ($animation-name: $test)
		body
				animation-name: test
		

@supports ($animation-name-test)
		body
				animation-name: test
		


// @at-root query
@at-root ($type: $value)
		.top-level
				background: pink
		

@at-root ($query)
		.top-level
				background: pink
		



/*
 * operations
 */

body
	// arithmetic operators
	width: (1px + (2em - 3rem)) * (4 / 5vh) % 6cm

	// plain css
	font: 10px/8px
	font: (italic bold 10px/8px)
	font: #{$font-size/#{$line-height}

	// division
	width: $width/2
	width: round(1.5)/2
	width: (500px/2)
	width: 5px + 8px/2px

	// minus sign
	animation-name: a-1 // identifier
	margin: (5px - 3px) 5px-3px 3-2 (1 -$var) // subtraction
	margin: 1 -3em // negative number
	margin: -$var -(1) // unary negation

	// string concatenation
	content: "Foo " + Bar // "Foo Bar"
	font-family: sans- + "serif" // sans-serif


// string concatenation in string-only context
@keyframes ('foo' + bar)
@keyframes (foo + "bar")

// comparison operators, logical operators
$a: (1 < 2 and 1 > 2) or (1 <= 2) and 1 >= 2
$b: not (1 == 2) and 1 != 2


/*
 * interpolation
 */

// selector
#{'#id',
#i#{'d',
#{'.whole-class',
.part-#{'class'-fragment,
#{'div',
d#{'i'v,
[#{'attr'=#{'value'],
[#{'attr="value"'],
#{'[attr="value"]',
#{'::selection',
::se#{'lect'ion,
#{':hover',
:ho#{'v'er,
#{':lang(fr)',
:la#{'ng(f'r),
:lang(#{'fr'),
:nth-child(#{'2n+1'),
:not(#{'div'),
#{'%placeholder',}
%pla#{'ceho'lder

	// property name
	#{'font': serif}
	background-#{'image': none
	border-#{'bottom'-width: 0}

	// property value
	font-size: #{$font-size}
	font-family: #{'arial, sans'-serif}
	width: #{5 * (3 - 1)px}

	// function name
	background: #{'url'('image.png')}
	background: u#{'r'l('image.png')}

	// !important (exclamation mark needs to be inside)
	width: 0 #{'!important'}
	width: 0 #{'!import'ant}

	// inside strings
	content: "#{$var"}"
	content: '#{$var'}'

	// inside comments
	/* multi-line: #{$yes} */
	// single line: #{$no}


// media type, feature test
@import "foo" #{'screen' and (#{'orientation'}: #{'landscape'})
// @media #{'screen' and (#{'orientation: landscape')
//		 width: 700px
// 

// font name, font feature custom name, font feature value
@font-feature-values #{'Font One'}
	@styleset // interpolation not accepted here?
		#{'nice-style': #{12}}
		


// inside @import url()
@import url("http://fonts.googleapis.com/css?family=#{$family}")

// @keyframes name, selector
@keyframes #{'myanim'}
	#{'from' width: 0}
	#{'to'	 width: 100%}


// @namespace name
@namespace #{'svg' url('http://www.w3.org/2000/svg')}

// @page custom name, pseudo-page
@page #{'toc'}, #{':first'}
	margin: 0

	@left-top // interpolation not accepted here?
		content: ''
		


// @supports test
@supports (#{'animation-name'}: #{'test'})
	body
		animation-name: test
		

// @supports (#{'animation-name: test')
//		 body
//				 animation-name: test
//		 
// 

// @at-root query
@at-root (#{'without'}: #{'media'})
	.top-level
		background: pink
		

@at-root (#{'without: media'})
	.top-level
		background: pink
	



/*
 * functions
 */

body
	// single line comments not parsed inside url()
	background: url(http://example.com/styles.css)

	// keyword arguments
	color: hsl($hue: 0, $saturation: 100%, $lightness: 50%)



/*
 * at-rules
 */

// @import
@import "rounded-corners", "text-shadow" // multiple files
#main // nested @import
	@import "example" // but not inside mixins or control directives


// nested @media
@media screen
	.sidebar
		@media (orientation: landscape)
			width: 500px
				
		


// @extend
#main
	@extend #hello

	// no error if not found
	@extend .from[the="other-side"] !optional

	// placeholder selector
	@extend div%placeholder


// @at-root
#main
	@at-root .child
		color: red
		

@media screen
	@supports (font-variant-alternates: styleset(nice-style))
		@at-root (without: media supports) // with query
			.absolutely-top-level
				background: pink
					
				
		


// @if/@else if/@else, @debug/@warn/@error
@if $num-errors == 0
		@debug "$num-errors is 0"
 @else if $num-errors > 0
		@warn "oops there are #{$num-errors errors}"
 @else
		@error "negative errors?!?"


// @for
@for $i from 1 through 3
	.item-#{$i}
		width: 2em * $i
		


// @each
@each $animal in puma, sea-slug, egret, salamander
	.#{$animal-icon}
		background-image: url('/images/#{$animal.png}')
		

@each $header, $size in (h1: 2em, h2: 1.5em, h3: 1.2em)
	#{$header}
		font-size: $size
		


// @while
$i: 6
@while $i > 0
		.item-#{$i}
				width: 2em * $i
		
		$i: $i - 2


// @mixin
@mixin large-text
	font-size: 128px

@mixin sexy-border($color, $width: 1in) // with arguments, default value
	border:
		color: $color
		width: $width
		

@mixin box-shadow($shadows...) // variable arguments
	box-shadow: $shadows

@mixin apply-to-ie6-only // accepts content block
	* html
		@content
		


// @include
.page-title
	@include large-text

p
	@include sexy-border(blue) // with arguments

div
	@include sexy-border($color: blue, $width: 10cm) // keyword arguments

.primary
	@include box-shadow($shadows...) // expand list into arguments

@include apply-to-ie6-only // passing content block
	display: block

	#main
		background: black
	


// @function
@function grid-width($n)
	@return $n * $grid-width + ($n - 1) * $gutter-width

#sidebar
	width: grid-width(5)




/*
 * test cases
 */

.declarations-or-selectors
	// declarations
	display:block
	font-family:arial
	font:
		family: fantasy
		weight: bold
	
	font: fantasy
		size: 20px
		style: italic
	
	#{$property}:block
	#{$property}:
		color: red
	
	color:#000
	width:#{$width}

	// incorrectly highlighted declarations
	display:block
	

	// selectors
	input:focus
		opacity: 0.5
	
	div:nth-child(2n+1)
		background-color: gray
	
	div:-moz-full-screen
		display: block
	
	a:#{$state}
		color: blue
	
	#{$selector}:focus
		color: blue
	



// from file.css

//@charset "UTF-8"
// Sass follows the CSS spec when reading stylesheets,
// so any @charset rule must be at the top of the document

/*
 * general
 */

/* whitespace */
#main
		color:aqua
		float: left!important
		margin	:	0	
		width
				:
				100%
				!
				important
				


/* case insensitivity */
Body
		FONT: 12Px/16pX iTaLiC sans-SERIF



/*
 * selectors
 */

/* simple selectors */
#testID,			 /* id */
.someclass,		/* class */
div,					 /* type */
*,						 /* universal */
[lang|="zh"] /* attribute */
		color: black


/* combinators */
header + main, /* adjacent sibling */
li ~ li,			 /* general sibling */
ul > li,			 /* child */
ul ul				/* descendant */
		color: blue


/* pseudo-elements */
body
	&:after,
	&::after,
	&::placeholder,
	&::selection
		color: green


/* pseudo-classes */
body
	&:hover,
	&:required,
	&:lang(fr),
	&:not(div#sidebar.fancy),
	&:nth-child(n+1),
	&:nth-last-child(-2n - 30),
	&:nth-of-type(5),
	&:nth-last-of-type(even)
			color: yellow


/* pseudo-classes with invalid arguments */
body
	&:not(div:before),				 /* pseudo-element */
	&:not(input::placeholder), /* pseudo-element */
	&:not(p:not(:lang(en))),	 /* nested :not */
	&:nth-child(1.2n),				 /* non-integer */
	&:nth-child(.5),					 /* non-integer */
	&:nth-child(n+-1)				/* number sign */
		color: red


/* namespace qualified */
a,					 /* type in default namespace */
svg|a,			 /* type in specified namespace */
|a,					/* type in no namespace */
*|a,				 /* type in all namespaces (including no namespace) */
svg|*,			 /* universal */
svg|[fill] /* attribute */
		color: white



/*
 * basic data types
 */

#main
	/* angle */
	transform: rotate(+33.333e+3deg)

	/* color */
	color: #f00
	color: #f00f /* #rgba */
	color: #ff0000
	color: #ff0000ff /* #rrggbbaa */
	color: red
	color: lightgoldenrodyellow
	color: rebeccapurple
	color: currentColor

	/* freqency (not currently used for any property) */
	content: 44.1kHz

	/* integer */
	z-index: +255
	z-index: -1

	/* length */
	width: 10px
	width: 10.5rem
	width: -10e-2vw

	/* number */
	opacity: .5
	opacity: 0.3333
	opacity: 1
	opacity: 2e-34

	/* percentage */
	width: 100%

	/* string */
	content: "double quoted"
	content: 'single quoted'

	/* time */
	transition-duration: .4s
	transition-duration: 400ms

	/* unicode range */
	unicode-range: U+0025-00FF
	unicode-range: U+4?? /* wildcard range */


/* ratio */
@media (min-aspect-ratio: 16/9)

/* resolution */
@media (min-resolution: +2.54dpcm)


/*
 * identifiers
 */

/* leading hyphens */
#-here.-there,
#-- .--everywhere /* two hyphens: https://stackoverflow.com/a/30822662 */
	color: blue


/* non-ASCII */
#español,
#你好,
.❤♫
	color: green


/* invalid identifiers */
#1id,			/* starts with digit */
.-2class /* starts with hyphen digit */
	color: maroon



/*
 * escaping
 */

/* selectors */
#\..\+\ space\@\>,														/* special character escape */
#\E9 dition .\0000E9dition .motion_\e9motion, /* Unicode character escape */
.\e33 div,																		/* trailing space terminates Unicode character escape */
.\e33	div,																	 /* extra space to denote separate tokens */
.\31 23																		 /* escape leading digit of identifier */

	/* property value */
	content: "\E9 dition \
						\"\0000E9dition\" \
						\e9motion"

	/* function name */
	background: \u\72\l(image.png)



/*
 * functions
 */

#main
	/* url */
	background: url("image.svg")

	/* function argument keywords */
	background-image: linear-gradient(to left top, #fff, blue)
	grid-template-columns: repeat(2, minmax(max-content, 300px) 1fr) 100px



/*
 * style properties
 */

#main
	/* svg */
	fill: url(#pattern)
	text-rendering: optimizeLegibility

	/* css3 */
	font-variant-east-asian: jis04
	size: letter
	transition-timing-function: ease-in

	/* animatable */
	transition-property: height, font-size, visibility


/*
 * modifiers
 */
body
	background: pink !important



/*
 * media queries
 */

@media screen, (orientation: portrait)
@media not (print and (min-monochrome: 16) and (color))
@media only screen @media not print


/*
 * at-rules
 */

/* @font-face */
@font-face
		font-family: MyHelvetica
		src: local("Helvetica Neue"),
				 local("HelveticaNeue"),
				 url(MgOpenModerna.ttf)


/* @font-feature-values */
@font-feature-values Font One
	@styleset
		nice-style: 12
		

.nice-look
	font-variant-alternates: styleset(nice-style)


/* @import */
@import URL("fineprint.css")
@import 'custom.css'
@import url('landscape.css') screen and (orientation: landscape), print

/* @keyframes */
@keyframes myanim
	from opacity: 0.0 
	50%	opacity: 0.5 
	to	 opacity: 1.0 


/* @media */
@media all
	body
		background: gray
	
	@media screen, (orientation: portrait)
		body
			background: grey
			
		


/* @namespace */
@namespace "http://www.w3.org/1999/xhtml"
@namespace svg url(http://www.w3.org/2000/svg)

/* @page */
@page
	bleed: 1cm

@page toc, :blank
	margin: 2cm
	marks: crop cross

@page index:left
	size: A4

	@top-right
		content: "Page " counter(page)
		


/* @supports */
@supports (animation-name: test)
	@keyframes 'my-complicated-animation'
		0%	 width: 0 
		100% width: 100% 
		



/*
 * vendor-specific extensions
 */

/* pseudo-elements */
input[type="number"]::-webkit-outer-spin-button,
input[type="number"]::-webkit-inner-spin-button
	-webkit-appearance: none

input[type="number"]
	-moz-appearance: textfield


/* pseudo-classes */
#page:-moz-full-screen,
#page:-ms-fullscreen,
#page:-webkit-full-screen
	background: silver


/* functions */
.footer
	background-image: -webkit-linear-gradient(to left top, #fff, blue)


/* style properties */
#sidebar
	-ms-overflow-style: -ms-autohiding-scrollbar

@supports not ((text-align-last: justify) or (-moz-text-align-last: justify))
	body
		text-align: justify
		






@charset "UTF-8"

@font-face
	font-family: 'MyWebFont'
	src:		url('myfont.woff2') format('woff2'),
					url('myfont.woff') format('woff')


@keyframes pulse
	0%
		background-color: #001f3f
	
	100%
		background-color: #ff4136
		


@page :first
	margin: 1in


@supports (display: flex)
	.module display: flex 


@supports (display: flex) and (-webkit-appearance: checkbox)
	.module display: flex 


// the @ is ilegal
@mixin my-padding-mixin($some-number,$somoOtherNumber,@-moz-document)
	padding: $some-number


@namespace url(http://www.w3.org/1999/xhtml)

@document 
	/* Rules for a specific page */
	url(http://css-tricks.com/),

	/* Rules for pages with a URL that begin with... */
	url-prefix(http://css-tricks.com/snippets/),

	/* Rules for any page hosted on a domain */
	domain(css-tricks.com),

	/* Rules for all secure pages */
	regexp("https:.*")

	/* Start styling */
	body font-family: Comic Sans 



@import "sample1", "sample2", "sample3"

.master
	color:	black
	font-size: 12px


.emphasis
	@extend .master
	font-weight: bold




.top
	.first
		font-size: 12px
	
	@at-root
		.second
			font-size: 14px
		
		.third
			font-size: 16px
			
	
	.fourth
		font-size: 18px
		


@media screen
	.main
		@media (max-width: 600px)
			width: 100%
		
		@media (min-width: 700px)
			width: 70%
			
		
	

/* at-rules */
@-webkit-keyframes myanim
	from opacity: 0.0 
	50%	opacity: 0.5 
	to	 opacity: 1.0 



.top
	.first
		font-size: 12px
	
	@at-root
		.second
			font-size: 14px
		
		.third
			font-size: 16px
		
	
	.fourth
		font-size: 18px
		

@media print
	.copy
		color: black
		@at-root (without: media)
			width: 100%
				
</style>