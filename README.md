# Video Doorbell, Lab 7

*A lab report by John Q. Student*

### In This Report

1. Upload a video of your version of the camera lab to your lab Github repository
1. As usual, update your class Hub repository to add your [forked IDD-Fa18-Lab7](/FAR-Lab/IDD-Fa18-Lab7) repository.
1. Answer the questions in-line below on your README.md.

## Part A. HelloYou from the Raspberry Pi

**a. Link to a video of your HelloYou sketch running.**

To begin, I put together the arduino setup described in the prelab and uploaded the HelloYou code. As can be seen in the video, when I click the button the serial monitor prints "Light." When I let go of the button, the serial monitor prints "Dark." When I connect to the web server, clicking the button causes the background of the web server to turn white and letting go of the button causes it to go back to black, as can be seen in the second video.

[HelloYou Video Serial Monitor](https://youtu.be/fPpvn2BmM3c)

[HelloYou Video Web Browser](https://youtu.be/lk9tCxKvQeA)

## Part B. Web Camera

**a. Compare `helloYou/server.js` and `IDD-Fa18-Lab7/pictureServer.js`. What elements had to be added or changed to enable the web camera? (Hint: It might be good to know that there is a UNIX command called `diff` that compares files.)**

To do this, I used both the diff function and also inspected the two files of code manually. Overall, there were many minor/syntax changes. For example, helloYou used “port.write” and “const” values, while pictureServer used “serial.write” and “var” values. 

I then focused on documenting the major code additions to better understand how the function of the code was changing. Upon inspection, I only found one non-syntactic line that was present in helloYou that was not present in pictureServer:

```js
const server = http.Server(app);        // connects http library to server
```
However, I was able to find several pieces of code that were added to pictureServer (present in pictureServer and not in helloYou). The first piece of code was the following, which loads the webcam in.

```js
var NodeWebcam = require( "node-webcam" );
```

The next chunk of code that was added to the pictureServer code was primarily for setting up the web cam and choosing how it will operate, such as the size of the picture, the delay, where to save the shots, output types, etc. The final line of code in this chunk simply starts up the web cam.

```js
var opts = { 
    width: 1280, //size
    height: 720,
    quality: 100,

    delay: 0,
    saveShots: true,
    output: "jpeg",

    device: false,
    callbackReturn: "location",
    verbose: false
};
var Webcam = NodeWebcam.create( opts ); 
```
The final chunk of code that was added is used for communicating with the web sockets / web browser. For example, it programs what to do when the user clicks on the 'Take a picture' button, which is to name the image, log it to the console, take the picture, and emit the picture. 

```js
  socket.on('takePicture', function() {
    var imageName = new Date().toString().replace(/[&\/\\#,+()$~%.'":*?<>{}\s-]/g, '');
    console.log('making a making a picture at'+ imageName); 
    NodeWebcam.capture('public/'+imageName, opts, function( err, data ) {
    io.emit('newPicture',(imageName+'.jpg')); 
  });
  });
```

**b. Include a video of your working video doorbell**

To create my video doorbell, I essentially played around with the code until I isolated the emit statement that was causing the screen to turn black and white when the button gets pressed. I commented this line out and replaced it with code for snapping a picture so that when the button gets pressed, a picture is taken. The full code (and video) are below, but a short snippet of the region I actually changed is embedded below as well: 

```js
serial.pipe(parser);
parser.on('data', function(data) {
  console.log('Data:', data);
//io.emit('server-msg', data);
  var imageName = new Date().toString().replace(/[&\/\\#,+()$~%.'":*?<>{}\s-]/g, '');
  console.log('making a making a picture at'+ imageName); 
  NodeWebcam.capture('public/'+imageName, opts, function( err, data ) {
  io.emit('newPicture',(imageName+'.jpg'));
});
});
```
As can be seen, the first 3 lines are unchanged. However, I comment out the fourth line and then add the code in for snapping a photo below in this block of code. 

[Video Doorbell Code](https://github.com/barkadosh1/IDD-Fa19-Lab7/blob/master/pictureServerUpdated.js)

[Video Doorbell Video](https://github.com/barkadosh1/IDD-Fa19-Lab7/blob/master/pictureServerUpdated.js)

## Part C. Make it your own

**a. Find, install, and try out a node-based library and try to incorporate into your lab. Document your successes and failures (totally okay!) for your writeup. This will help others in class figure out cool new tools and capabilities.**

**b. Upload a video of your working modified project**
