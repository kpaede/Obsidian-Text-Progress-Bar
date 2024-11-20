# Obsidian-Text-Progress-Bar
A dynamic DataviewJS script for Obsidian that visualizes character count progress with a customizable progress bar and goal markers.

## Usage
1. Ensure you have the [Dataview plugin](https://github.com/blacksmithgu/obsidian-dataview) enabled in Obsidian.
2. Paste the script into a DataviewJS code block in your note:

```dataviewjs
const minChars = 7000; // Minimum character goal
const maxChars = 8000; // Max character goal
const exceedColor = "#32CD32"; // Color after exceeding senseful goal

const filePath = dv.current().file.path;
const fileContent = await app.vault.adapter.read(filePath);

// Step 1: Exclude content inside code blocks
let filteredContent = fileContent.replace(/```[\s\S]*?```/g, "");

// Step 2: Remove non-visible portions of Markdown links
filteredContent = filteredContent.replace(/\[.*?\]\(.*?\)/g, (match) => {
    const visiblePart = match.match(/\[(.*?)\]/);
    return visiblePart ? visiblePart[1] : "";
});

// Step 3: Calculate character count
const charCount = filteredContent.length;

// Step 4: Determine the bar color
let progressColor;
if (charCount < minChars) {
    progressColor = "red"; // Below minimum goal
} else if (charCount < maxChars) {
    progressColor = "lightgreen"; // Between minimum and senseful goal
} else {
    progressColor = exceedColor; // Beyond senseful goal
}

// Step 5: Create the progress bar container
const progressContainer = document.createElement("div");
progressContainer.style.position = "relative";
progressContainer.style.width = "100%";
progressContainer.style.height = "30px";
progressContainer.style.border = "1px solid #ccc";
progressContainer.style.borderRadius = "5px";
progressContainer.style.overflow = "hidden";
progressContainer.style.backgroundColor = "#f9f9f9";

// Step 6: Create the progress bar
const progressBar = document.createElement("div");
progressBar.style.height = "100%";
progressBar.style.width = `${Math.min((charCount / maxChars) * 100, 100)}%`;
progressBar.style.backgroundColor = progressColor;
progressBar.style.transition = "width 0.3s ease";
progressContainer.appendChild(progressBar);

// Step 7: Add goal markers
function addMarker(goal, color) {
    const marker = document.createElement("div");
    marker.style.position = "absolute";
    marker.style.left = `${(goal / maxChars) * 100}%`;
    marker.style.top = "0";
    marker.style.height = "100%";
    marker.style.width = "2px";
    marker.style.backgroundColor = color;
    marker.title = `${goal} characters`;
    progressContainer.appendChild(marker);

    // Add text above the marker
    const markerText = document.createElement("span");
    markerText.style.position = "absolute";
    markerText.style.left = `${(goal / maxChars) * 100}%`;
    markerText.style.top = "-20px";
    markerText.style.transform = "translateX(-50%)";
    markerText.style.fontSize = "10px";
    markerText.style.color = color;
    markerText.textContent = `${goal}`;
    progressContainer.appendChild(markerText);
}

// Add markers for min and max goals
addMarker(minChars, "red");
addMarker(maxChars, "green");

// Step 8: Display the progress bar and character count
dv.container.appendChild(progressContainer);
dv.paragraph(`${charCount} / ${minChars} Zeichen inkl. Leerzeichen`);
```

   
3. Open a note, and the script will:
- Display a progress bar based on the character count.
- Show a textual summary of your character count versus the minimum goal.

## Customization
You can modify the following settings in the script:
- **Minimum Character Goal**: Adjust `minChars` to set the lower bound.
- **Maximum Character Goal**: Adjust `maxChars` to set the upper bound.
- **Exceed Color**: Customize the color for progress exceeding the maximum (`exceedColor`).

Example:
```javascript
const minChars = 7000; // Minimum character goal
const maxChars = 8000; // Maximum character goal
const exceedColor = "#32CD32"; // Color after exceeding the goal

