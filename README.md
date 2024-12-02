<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Teachable Machine Image Model</title>
    <style>
        /* Global reset */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        /* Body and overall layout */
        body {
            font-family: Arial, sans-serif;
            background-color: #f1f1f1;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            flex-direction: column;
            color: #333;
        }

        h1 {
            color: #2c3e50;
            margin-bottom: 20px;
            font-size: 2rem;
            text-align: center;
        }

        /* Button styling */
        button {
            padding: 15px 30px;
            font-size: 1.2rem;
            border: none;
            border-radius: 8px;
            background: linear-gradient(45deg, #6a11cb, #2575fc);
            color: white;
            cursor: pointer;
            transition: all 0.3s ease;
            margin-top: 20px;
        }

        button:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 15px rgba(0, 0, 0, 0.2);
        }

        button:focus {
            outline: none;
        }

        /* Webcam container */
        #webcam-container {
            margin-top: 30px;
            border: 5px solid #2980b9;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
            padding: 10px;
            background-color: #fff;
        }

        /* Label container */
        #label-container {
            margin-top: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        /* Label styling */
        #label-container div {
            font-size: 1.2rem;
            color: #34495e;
            padding: 10px 20px;
            margin: 5px;
            background-color: #ecf0f1;
            border-radius: 5px;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
            width: 100%;
            max-width: 400px;
            text-align: center;
        }

        /* Add some effects */
        #label-container div:hover {
            background-color: #bdc3c7;
            cursor: pointer;
            transform: scale(1.05);
            transition: transform 0.2s ease-in-out;
        }
    </style>
</head>
<body>
    <h1>Teachable Machine Image Model</h1>
    <button type="button" onclick="init()">Start</button>
    <div id="webcam-container"></div>
    <div id="label-container"></div>

    <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@latest/dist/tf.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@teachablemachine/image@latest/dist/teachablemachine-image.min.js"></script>

    <script type="text/javascript">
        // The link to your model provided by Teachable Machine export panel
        const URL = "./my_model/"; 

        let model, webcam, labelContainer, maxPredictions;

        // Load the image model and setup the webcam
        async function init() {
            const modelURL = URL + "model.json";
            const metadataURL = URL + "metadata.json";

            // Load the model and metadata
            model = await tmImage.load(modelURL, metadataURL);
            maxPredictions = model.getTotalClasses();

            // Convenience function to setup a webcam
            const flip = true; // whether to flip the webcam
            webcam = new tmImage.Webcam(200, 200, flip); // width, height, flip
            await webcam.setup(); // request access to the webcam
            await webcam.play();
            window.requestAnimationFrame(loop);

            // Append elements to the DOM
            document.getElementById("webcam-container").appendChild(webcam.canvas);
            labelContainer = document.getElementById("label-container");
            for (let i = 0; i < maxPredictions; i++) { // and class labels
                labelContainer.appendChild(document.createElement("div"));
            }
        }

        async function loop() {
            webcam.update(); // update the webcam frame
            await predict();
            window.requestAnimationFrame(loop);
        }

        // Run the webcam image through the image model
        async function predict() {
            // predict can take in an image, video or canvas HTML element
            const prediction = await model.predict(webcam.canvas);
            for (let i = 0; i < maxPredictions; i++) {
                const classPrediction = prediction[i].className + ": " + prediction[i].probability.toFixed(2);
                labelContainer.childNodes[i].innerHTML = classPrediction;
            }
        }
    </script>
</body>
</html>
