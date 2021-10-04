# img2img. PHP Converter, sampler of pdfs & psd, do resizes & some filters in images as SEPIA

## V.1.0.0

This class in pure PHP try to manage all types of images. Also can read pdf's or PSD (photoshop) files to take previews of the documents. Can create WEBP files. It try to autosearch format of image with some methods. At least will try with file extension.

# SAMPLE:
![Sample of filter SEPIA](https://github.com/vivesweb/img2img/blob/main/img2img_result_3.jpg?raw=true)

Sample of filter SEPIA


 # REQUERIMENTS:
 
    - A minimum (minimum, minimum, minimum requeriments is needed). Tested on:
 		
    - Simple Raspberry pi (B +	512MB	700 MHz ARM11) with Raspbian Lite PHP7.3 (i love this gadgets)  :heart_eyes:
 		
    - VirtualBox Ubuntu Server 20.04.2 LTS (Focal Fossa) with PHP7.4.3 
    
    - Ubuntu 20.04.3 LTS (Focal Fossa). Laptop Acer Extensa 5630 with PHP 7.4.3 (cli) (built: Aug 13 2021 05:39:12) ( NTS )

 # SERVER REQUERIMENTS:
 
    - PHP with GD enabled: sudo apt install php-gd
    
    - PHP with Imagick for some functions. It is not required for by most methods, but in some, like reading pdf or psd, you will need: sudo apt install php-imagick
    
    - For open pdf's, if you get attempt to perform an operation not allowed by the security policy `PDF'
       Add 
       
       < policy domain="coder" rights="read | write" pattern="PDF" />
       
       just before </policymap> in /etc/ImageMagick-7/policy.xml
       - Change ImageMagick-7 with your Imagick version
 
 
  # FILES:
 There are 3 basic files:
 
 *img2img.class.php* -> **Master class**. This file is the main file that you need to include in your code.
 
 *mime_types.php* -> **Mime Types**. Is included inside img2img.class.php
 
 *example.php* -> **example file**
 
 *source_example_psd.psd, source_example_pdf.pdf, source_example.jpg* are original images to use to reproduce test
 
 *img2img_resultxxx.xxx* are examples of the result that you can see at exemple.php
 
  # INSTALLATION:
 A lot of easy :smiley:. It is written in PURE PHP. Only need to include the files. Tested on basic PHP installation
 
         require_once( 'img2img.class.php' );
	 
 
 # BASIC USAGE:
 
         $img2img = new img2img( __DIR__.'/source_example.jpg' );
         $img2img->filter( IMG_FILTER_SEPIA, 4, 80 );
         $img2img->save( '/tmp/img2img_result_3.jpg' );
 
 
# DEFAULTS:

You can set the sizes that you normally work:

		'arr_thb_default_sizes' => [ '2048x1152' => [2048, 1152],  '1920x1080' => [1920, 1080], '1366x768' => [1366, 768], '640x480' => [640, 480], '512x384' => [512, 384], '320x240' => [320, 240], '200x150' => [200, 150] ],
		
Define the quality for your images. By default 100 (best quality but more file size)

		'quality' => 100, // 100 = best (more file size), 0 = poor (less file size)
		
You can define your working directory for temporary files. Default /tmp

		'tmpdir' => '/tmp',
		
For Pdf's you can set the dpis: Default 300 dpis

		'pdfresolution' => 300, // When open pdf file for capture page as image
		
You can autodetect the file format. Is unset by default. Some files take errors, but if you want to try to autodetect by the system, set it to true

		'autodetect_mimetypes' => false, // See notes at file_extension()
		
You can to see the errors or hide it. By default is set to SHOW ERRORS

		'debug' => true // Set to true to see errors


# RESUME OF METHODS:

- **CREATE IMG2IMG:**
 
*$img2img= new img2img( $path_to_file, $page_pdf );* // page_pdf only when load pdf's. It say what number of page we want to take image

Example:

         $img2img = new img2img( __DIR__.'/source_example.jpg' );



- **SET OBJECT GD:**
 
 If you have a GD Object in your program, you can assign directly to the class
 
*set_gd( $gd )*

Example:

        $img2img->set_gd( $gd );



- **CREATE THUMBNAIL:**
 
You can create a thumbnail with the id of default sizes or you can give your own size. You can to preserve the aspect ratio or not
Sizes: [ '2048x1152' => [2048, 1152],  '1920x1080' => [1920, 1080], '1366x768' => [1366, 768], '640x480' => [640, 480], '512x384' => [512, 384], '320x240' => [320, 240], '200x150' => [200, 150] ]
 
*thumb( $thumb, $preserve_aspect_ratio= true )*

Example:

         $img2img->thumb( 3 ); // 640x480
         
         $img2img->thumb( '640x480' ); // 640x480
         
         $img2img->thumb( '640x200', false ); // 640x200 without maintain aspect ratio
         



- **RESAMPLE:**
 
Is equal as thumb
 
*resample( $x, $y, $preserve_aspect_ratio= true )*

Example:

        $img2img->resample( 640, 480 ); // 640x480



- **SAVE:**

  You can save it at any available format. In de $cfg you define some options:
  
  - $cfg['type']: Format as 'jpg', 'gif', 'png'. ex: $cfg = ['jpg']
    if you do not give it, the system get the type through the file extension
  - $cfg['quality']: used in webp, avif, jpg format. 100 is best quality & mor size. 0 is less quality & less size. Default 80
  - $cfg['compressed']: [true|false] used in bmp format
  - $cfg['chunk_size']: [true|false] used in gd2 format. Default 128
  - $cfg['gd2_type']:  [IMG_GD2_RAW | IMG_GD2_COMPRESSED] used in gd2 format. Default IMG_GD2_RAW
  - $cfg['foreground']:  Hex color code. Used in wbmp & xbm format. Default '#000000'
  
  

*save( $filename, $cfg = null )*

Example:

       $img2img->save( '/tmp/img2img_result_4.jpg' );
	
	
- **GET THE CONTENT IN BASE64:**

Used normally to write directly to the browser. You can specify the format ('png', 'jpg', ....)

*base64( $format );*

Example:

       $img2img->base64( 'jpg' );
	
	
- **GET THE CONTENT IN RAW:**

Idem as base64, but the stream will be raw data. You can specify the format ('png', 'jpg', ....)

*raw( $format );*

Example:

       $img2img->raw( 'jpg' );
	
	
- **FLIP THE IMAGE HORIZONTAL, VERTICAL OR BOTH:**

You can to flip the image in IMG_FLIP_HORIZONTAL | IMG_FLIP_VERTICAL | IMG_FLIP_BOTH. Default IMG_FLIP_HORIZONTAL

*flip( $type = IMG_FLIP_HORIZONTAL );*

Example:

       $img2img->flip( );



- **FILTER:**

You can apply filters to the image, as SEPIA, BLACK & WHITE, OR OTHER NORMAL FILTERS

*filter( $filtertype, $arg1 = null, $arg2 = null, $arg3 = null, $arg4 = null )*

filter can be one of the following:
	 
	 * IMG_FILTER_NEGATE: Reverses all colors of the image.
	 * IMG_FILTER_GRAYSCALE: Converts the image into grayscale by changing the red, green and blue components to their weighted sum using the same coefficients as the REC.601 luma (Y') calculation. The alpha components are retained. For palette images the result may differ due to palette limitations.
	 * IMG_FILTER_BRIGHTNESS: Changes the brightness of the image. Use args to set the level of brightness. The range for the brightness is -255 to 255.
	 * IMG_FILTER_CONTRAST: Changes the contrast of the image. Use args to set the level of contrast.
	 * IMG_FILTER_COLORIZE: Like IMG_FILTER_GRAYSCALE, except you can specify the color. Use args, arg2 and arg3 in the form of red, green, blue and arg4 for the alpha channel. The range for each color is 0 to 255.
	 * IMG_FILTER_EDGEDETECT: Uses edge detection to highlight the edges in the image.
	 * IMG_FILTER_EMBOSS: Embosses the image.
	 * IMG_FILTER_GAUSSIAN_BLUR: Blurs the image using the Gaussian method.
	 * IMG_FILTER_SELECTIVE_BLUR: Blurs the image.
	 * IMG_FILTER_MEAN_REMOVAL: Uses mean removal to achieve a "sketchy" effect.
	 * IMG_FILTER_SMOOTH: Makes the image smoother. Use args to set the level of smoothness.
	 * IMG_FILTER_PIXELATE: Applies pixelation effect to the image, use args to set the block size and arg2 to set the pixelation effect mode.
	 * IMG_FILTER_SCATTER: Applies scatter effect to the image, use args and arg2 to define the effect strength and additionally arg3 to only apply the on select pixel colors.
	 * IMG_FILTER_SEPIA: Use arg1 to use between different types of sepia and arg2 & arg3 to define de effect
	 * IMG_FILTER_BLACK_WHITE: Use to create a black & white image
	 * IMG_FILTER_VIGNETTE: arg1: blackPoint, arg2: $whitePoint, arg3: $x, arg4: $y
	 * args
	 * IMG_FILTER_BRIGHTNESS: Brightness level.
	 * IMG_FILTER_CONTRAST: Contrast level.
	 * IMG_FILTER_COLORIZE: Value of red component.
	 * IMG_FILTER_SMOOTH: Smoothness level.
	 * IMG_FILTER_PIXELATE: Block size in pixels.
	 * IMG_FILTER_SCATTER: Effect substraction level. This must not be higher or equal to the addition level set with arg2.
	 * IMG_FILTER_SEPIA:	Define type of sepia
	 * arg2
	 * IMG_FILTER_COLORIZE: Value of green component.
	 * IMG_FILTER_PIXELATE: Whether to use advanced pixelation effect or not (defaults to false).
	 * IMG_FILTER_SCATTER: Effect addition level.
	 * IMG_FILTER_SEPIA: if (arg1 == 2) Defines % of sepia. if(arg1 == 4): Define tone of sepia in Imagick sepiaToneImage()
	 * arg3
	 * IMG_FILTER_COLORIZE: Value of blue component.
	 * IMG_FILTER_SCATTER: Optional array indexed color values to apply effect at.
	 * arg4
	 * IMG_FILTER_COLORIZE: Alpha channel, A value between 0 and 127. 0 indicates completely opaque while 127 indicates completely transparent.

Example:

       $img2img->filter( IMG_FILTER_SEPIA, 4, 80 );


 **Of course. You can use it freely :vulcan_salute::alien:**
 
 By Rafa.
 
 
 @author Rafael Martin Soto
 
 @author {@link http://www.inatica.com/ Inatica}
 
 @blog {@link https://rafamartin10.blogspot.com/ Rafael Martin's Blog}
 
 @since OCTOBER 2021
 
 @version 1.0.0
 
 @license GNU General Public License v3.0

