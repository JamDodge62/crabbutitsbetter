# crabbutitsbetter
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Crab Detector</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div class="container">
        <h1>How Crab Are You?</h1>
        <p>Upload an image to see how "orange" it is!</p>
        <input type="file" id="imageInput" accept="image/*" />
        <canvas id="canvas" style="display:none;"></canvas>
        <div id="result">
            <p id="score">Upload an image to get started!</p>
            <p id="crabLevel"></p>
        </div>
    </div>

    <script src="script.js"></script>
</body>
</html>

body {
    font-family: Arial, sans-serif;
    background-color: #f4f4f9;
    color: #333;
    text-align: center;
    padding: 20px;
}

.container {
    max-width: 600px;
    margin: 0 auto;
    background-color: white;
    padding: 30px;
    border-radius: 10px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

h1 {
    color: #ff7f50;
}

input[type="file"] {
    padding: 10px;
    margin: 20px 0;
    background-color: #ff7f50;
    color: white;
    border: none;
    cursor: pointer;
    border-radius: 5px;
}

input[type="file"]:hover {
    background-color: #ff5733;
}

#result {
    font-size: 18px;
    margin-top: 20px;
}

#crabLevel {
    font-weight: bold;
    font-size: 24px;
}

document.getElementById("imageInput").addEventListener("change", handleImageUpload);

function handleImageUpload(event) {
    const file = event.target.files[0];
    if (file) {
        const img = new Image();
        const reader = new FileReader();
        reader.onload = function(e) {
            img.src = e.target.result;
        };
        reader.readAsDataURL(file);

        img.onload = function() {
            const canvas = document.getElementById("canvas");
            const ctx = canvas.getContext("2d");
            canvas.width = img.width;
            canvas.height = img.height;
            ctx.drawImage(img, 0, 0);

            // Get pixel data from the canvas
            const imageData = ctx.getImageData(0, 0, img.width, img.height);
            const pixels = imageData.data;

            let orangeCount = 0;
            let totalPixels = pixels.length / 4;

            // Loop through each pixel and count how "orange" it is
            for (let i = 0; i < pixels.length; i += 4) {
                const r = pixels[i];     // Red
                const g = pixels[i + 1]; // Green
                const b = pixels[i + 2]; // Blue

                // Check if the pixel is within the "orange" range
                if (r > 150 && g > 100 && g < 180 && b < 100) {
                    orangeCount++;
                }
            }

            // Calculate the percentage of orange pixels
            const orangePercentage = (orangeCount / totalPixels) * 100;
            updateResult(orangePercentage);
        };
    }
}

function updateResult(orangePercentage) {
    const scoreElement = document.getElementById("score");
    const crabLevelElement = document.getElementById("crabLevel");

    scoreElement.innerText = `Orange Level: ${orangePercentage.toFixed(2)}%`;

    if (orangePercentage > 60) {
        crabLevelElement.innerText = "You're a very crabby crab!";
        crabLevelElement.style.color = "red";
    } else if (orangePercentage > 30) {
        crabLevelElement.innerText = "You're somewhat crabby.";
        crabLevelElement.style.color = "orange";
    } else {
        crabLevelElement.innerText = "You're not very crabby...";
        crabLevelElement.style.color = "green";
    }
}

