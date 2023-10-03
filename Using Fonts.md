While SplashKit can draw text on graphical windows on its own, only the generic 8x8 bitmap font is bundled with the library with no scaling:
![[Pasted image 20231003005746.png]]
While this is generally sufficient for getting started with graphics, as you build your graphical program to be more sophisticated, this will not be enough, and you may want to supply your own fonts and set your own font sizes, so the testing screen above can look something like this:
![[Pasted image 20231003100214.png]]
In which case, SplashKit has got you covered! In this article, you will learn how to use custom fonts in SplashKit for drawing text, as well as a couple of tips for that.
# Getting started
The first and most important thing that you need is, well, fonts! SplashKit supports TrueType (`.ttf`) and OpenType (`.otf`) font formats, and can therefore handle most fonts, so you are not limited in this regard. Just pick a font and use it!
Here are some suggestions:
 - For a retro computing feel, there is [The Ultimate Oldschool PC Font Pack](https://int10h.org/oldschool-pc-fonts/fontlist/), which contains the fonts used by computers in the 80s and 90s (and yes, that's the MS-DOS font!).
 - If you are a Melbourne public transport enthusiast (just like myself!), and you for some reason want to replicate the train passenger information displays, you may want to check out the [Melbourne's Public Transport Gallery font collection](https://melbournesptgallery.weebly.com/fonts.html).
***Note:** Neither the author of the article or SplashKit is affiliated with any of the websites above. Also, if you plan to publish your application, be sure to check your fonts' licensing!*
Once you have picked your fonts, you need to prepare the resources directories (if you have not done that previously). To do so, use this command in your project's root directory:
```
skm resources
```
Now you should have a bunch of directories in there, in addition to the `include` and `.vscode` directories. Add your fonts into the `fonts` directory, and you are (almost) ready to go.
There are two ways to load fonts in SplashKit. You can either do that within the program's code using the [`load_font`](https://splashkit.io/api/graphics/#load-font) function:
```c++
font game_font = load_font("GameFont", "myfont.ttf");
```
Or, even better, make use of SplashKit's [resource bundle](https://splashkit.io/articles/guides/tags/starter/bundles/) feature by adding a line like this into your resource bundle file:
```
FONT,GameFont,myfont.ttf
```
***Note:** SplashKit's resource bundle tutorial states that fonts must be followed by their point sizes, but in reality, it is not required, and the font will still work normally. However, it may impact the font's clarity among other things, so it probably should be specified to be on the safe side.*
# Using fonts
Now, let's talk about actually using custom fonts in your SplashKit application. SplashKit refers to fonts using the `font` data type, which can be retrieved during loading like above, or given the font's name using the [`font_named`](https://splashkit.io/api/graphics/#font-named) function:
```c++
font game_font = font_named("GameFont");
```
***Note:** It is generally good practice to get the font data for each font once then store them somewhere for future use, instead of repeatedly looking them up by their names.*
## Font styles
SplashKit supports setting and getting font styles (i.e. bold/italic/underlined) via the [`set_font_style`](https://splashkit.io/api/graphics/#group-set-font-style) and [`get_font_style`](https://splashkit.io/api/graphics/#group-get-font-style) functions. Both functions allow fonts to be specified by their `font` struct or their name strings, which is rather convenient. The font style is stored in the `font_style` data type with the values `NORMAL_FONT`, `BOLD_FONT`, `ITALIC_FONT` and `UNDERLINE_FONT`, which can be bitwise OR-ed to combine different fonts. For example, the following code sets the font to be both bold and underlined:
```c++
set_font_style(game_font, BOLD_FONT | UNDERLINE_FONT);
```
And this code checks if the font is set to bold:
```c++
if(get_font_style("GameFont") & BOLD_FONT)
	write_line("Font is bold");
else
	write_line("Font is not bold");
```
## Drawing text
To draw text using custom fonts, you can use the following [`draw_text`](https://splashkit.io/api/graphics/#group-draw-text) function overloads:
```c++
void draw_text(const string &text, const color &clr, const string &fnt, int font_size, double x, double y);
void draw_text(const string &text, const color &clr, const string &fnt, int font_size, double x, double y, const drawing_options &opts);
void draw_text(const string &text, const color &clr, font fnt, int font_size, double x, double y);
void draw_text(const string &text, const color &clr, font fnt, int font_size, double x, double y, const drawing_options &opts);
```
The function (as well as many font-related functions in SplashKit) also accepts fonts either by their `font` struct or their names; however, it is best to use the former as it reduces lookup time. In addition to the font name/struct parameter `fnt`, there is also the `font_size` parameter, which allows you to specify the font's size (i.e. its height) in pixels.
So, for example:
```c++
draw_text("Hello, World!", COLOR_WHITE, game_font, 16, 0, 0);
```
## Text width and height
Unlike the default font, whose character sizes are fixed at 8x8 pixels, custom fonts vary in sizes, so it would be handy if we can know how much space the text will occupy beforehand. Luckily, SplashKit contains the functions [`text_width`](https://splashkit.io/api/graphics/#group-text-width) and [`text_height`](https://splashkit.io/api/graphics/#group-text-height) for doing this. For your reference, the library declares these functions as follows:
```c++
int text_width(const string &text, const string &fnt, int font_size);
int text_width(const string &text, font fnt, int font_size);

int text_height(const string &text, const string &fnt, int font_size);
int text_height(const string &text, font fnt, int font_size);
```
These functions return the text's width and height in pixels if it was drawn using the font and font size given in the `fnt` and `font_size` parameters. This will come in handy later, as you will see in the next section.
# Centering text
Let's say you want to draw your text in the centre of the window. The text would need to be placed at the window's centre minus half of the text's width/height, or in other words:
```c++
x = (window_width - text_width) / 2
y = (window_height - text_height) / 2
```
The window's width and height can easily be retrieved, either using the [`window_width`](https://splashkit.io/api/windows/#group-window-width) and [`window_height`](https://splashkit.io/api/windows/#group-window-height) functions (or [`current_window_width`](https://splashkit.io/api/windows/#current-window-width) and [`current_window_height`](https://splashkit.io/api/windows/#current-window-height)), or by simply storing the width and height previously supplied into [`open_window`](https://splashkit.io/api/windows/#open-window) somewhere else. In addition, as mentioned before, the text's width and height can be retrieved using the [`text_width`](https://splashkit.io/api/graphics/#group-text-width) and [`text_height`](https://splashkit.io/api/graphics/#group-text-height) functions. Their combined use allows us to achieve drawing centered text, which is one of the uses for those functions.

This should be enough information to get you started with using custom fonts in SplashKit; however, if there is something that was not mentioned here, you can check out [the graphics API documentation](https://splashkit.io/api/graphics/) for more information and details. Happy drawing!