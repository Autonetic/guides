# How To Generate A Captcha Image In PHP

To generate a CAPTCHA image in PHP from a class, you can encapsulate the CAPTCHA generation logic into a class. Below is an example of how you can structure such a class:

```php
class CaptchaGenerator {
    private $permitted_chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
    private $captcha_string;
    private $image;

    public function __construct() {
        $this->captcha_string = $this->generate_string($this->permitted_chars, 6);
        $this->image = imagecreatetruecolor(200, 50);
        imageantialias($this->image, true);
        $this->create_background();
        $this->add_noise();
        $this->write_text();
    }

    private function generate_string($input, $strength = 6) {
        $input_length = strlen($input);
        $random_string = '';
        for ($i = 0; $i < $strength; $i++) {
            $random_character = $input[mt_rand(0, $input_length - 1)];
            $random_string .= $random_character;
        }
        return $random_string;
    }

    private function create_background() {
        $colors = [];
        $red = rand(125, 175);
        $green = rand(125, 175);
        $blue = rand(125, 175);
        for ($i = 0; $i < 5; $i++) {
            $colors[] = imagecolorallocate($this->image, $red - 20*$i, $green - 20*$i, $blue - 20*$i);
        }
        imagefill($this->image, 0, 0, $colors[0]);
        for ($i = 0; $i < 10; $i++) {
            imagesetthickness($this->image, rand(2, 10));
            $rect_color = $colors[rand(1, 4)];
            imagerectangle($this->image, rand(-10, 190), rand(-10, 10), rand(-10, 190), rand(40, 60), $rect_color);
        }
    }

    private function add_noise() {
        $black = imagecolorallocate($this->image, 0, 0, 0);
        $white = imagecolorallocate($this->image, 255, 255, 255);
        $textcolors = [$black, $white];
        $fonts = [dirname(__FILE__) . '\\fonts\\Acme.ttf', dirname(__FILE__) . '\\fonts\\Ubuntu.ttf', dirname(__FILE__) . '\\fonts\\Merriweather.ttf', dirname(__FILE__) . '\\fonts\\PlayfairDisplay.ttf'];
        $string_length = strlen($this->captcha_string);
        $letter_space = 170 / $string_length;
        $initial = 15;
        for ($i = 0; $i < $string_length; $i++) {
            imagettftext($this->image, 20, rand(-15, 15), $initial + $i * $letter_space, rand(20, 40), $textcolors[rand(0, 1)], $fonts[array_rand($fonts)], $this->captcha_string[$i]);
        }
    }

    public function output_image() {
        header('Content-type: image/png');
        imagepng($this->image);
        imagedestroy($this->image);
    }
}
```

This class, `CaptchaGenerator`, encapsulates the entire process of creating a CAPTCHA image. It initializes the CAPTCHA string, creates a background, adds noise, and outputs the final image. To use this class, you would instantiate it and call the `output_image()` method to generate and display the CAPTCHA image.