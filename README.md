# Chrome-Extension-for-Polite-IT
The Polite-IT Chrome Extension helps users refine their messages in real-time, ensuring clear, professional, and polite communication. It solves tone miscommunication issues, making emails, chats, and documents more effective and stress-free. (should work for this MVP in Gmail, WhatsApp web and facebook natively, on top of the pop up window feature)


### **Project Details**  
- **Scope:** Develop a Chrome Extension based on our provided documentation, prototype, and end-product visuals.  
- **Goal:** Deliver a polished, user-friendly, and fully functional extension ready for deployment.  
- **Timeline:** Flexible but prefer a detailed timeline and milestones from applicants.  

### **Resources Provided**  
1. **Documentation**: https://brave-boa-add.notion.site/Overview-1636f67d2a4380cea0c2f31b13b2e4cc?pvs=4

2. **Prototype**: https://polite-scribe-gentle-touch.lovable.app/

3. **End Product Visualization**:
GIFs Showing Final Product: https://www.dropbox.com/scl/fo/xg4us5gnnugm3pfdc3jmt/AAeY1z7cDPyulIWd1kQ1CJc?rlkey=vejealdg7f6kw22fdv5s1vyfi&dl=0).  

chat gpt custom agent setup: https://www.dropbox.com/s/u2dox96p8fvr55z/Polite%20It%20Chat%20gpt%20example.mp4?dl=0

figma: https://www.figma.com/design/2sdSF1pixOZ4A3wDwBWJet/PoliteIt?node-id=463-80&t=qOXOlXOfFoahzT8Y-1

### **Requirements**  
- **Experience** in Chrome Extension development.  
- **Proficiency** in front-end technologies like JavaScript, HTML, and CSS.  
- Familiarity with **APIs** and browser extension architecture.  
- Strong **UI/UX skills** to replicate and improve upon the provided prototype.  
- Ability to write clean, efficient, and well-documented code.  
- Good communication skills and ability to provide regular updates.  

### **Deliverables**  
1. A fully functional Chrome Extension that matches the prototype and GIFs.  
2. Integration with necessary APIs as described in the documentation.  
3. Documentation for installation, usage, and future development.  
4. Testing and debugging for a smooth, error-free user experience.  

### **Nice to Have**  
- Experience with browser extension development for other browsers (Firefox, Edge).  
- Suggestions for additional features or improvements to the provided design.  
------
Creating a Chrome Extension like Polite-IT that helps users refine their messages in real-time requires various key functionalities, such as tone analysis, message improvement suggestions, and integration with platforms like Gmail, WhatsApp Web, and Facebook. Below, I will outline the essential components and provide code snippets that cover the core features, including integrating with APIs, Chrome extension architecture, and front-end UI/UX considerations.
Key Features for Polite-IT Chrome Extension

    Tone Analysis: Analyze the tone of a user's message and suggest improvements.
    Message Refinement: Offer suggestions to make the communication more polite, professional, or clear.
    Pop-up Window for Feedback: Provide a pop-up that offers tone feedback and message improvement suggestions.
    Integration with Gmail, WhatsApp Web, and Facebook: Allow users to refine their messages within these platforms.

1. Chrome Extension Structure

For simplicity, here's an overview of the folder structure of your extension:

/polite-it-extension
│
├── manifest.json              # Extension metadata
├── background.js              # Background script for managing extension lifecycle
├── popup.html                 # Pop-up UI
├── popup.js                   # JavaScript for pop-up functionality
├── content.js                 # Content script for injecting functionality into web pages
├── styles.css                 # CSS for styling the pop-up window
├── tone-analysis-api.js       # Tone analysis API interaction
├── icons/                     # Icons for the extension
│
└── README.md                  # Documentation

2. manifest.json

This file contains the metadata and permissions for the Chrome Extension. It will request permissions to interact with Gmail, WhatsApp Web, and Facebook.

{
  "manifest_version": 3,
  "name": "Polite-IT",
  "version": "1.0",
  "description": "Refine your messages in real-time for clearer, more polite communication.",
  "permissions": [
    "activeTab",
    "storage",
    "identity",
    "tabs",
    "https://mail.google.com/*",
    "https://web.whatsapp.com/*",
    "https://www.facebook.com/*"
  ],
  "background": {
    "service_worker": "background.js"
  },
  "action": {
    "default_popup": "popup.html",
    "default_icon": {
      "16": "icons/icon16.png",
      "48": "icons/icon48.png",
      "128": "icons/icon128.png"
    }
  },
  "content_scripts": [
    {
      "matches": [
        "https://mail.google.com/*",
        "https://web.whatsapp.com/*",
        "https://www.facebook.com/*"
      ],
      "js": ["content.js"]
    }
  ],
  "host_permissions": [
    "http://*/*",
    "https://*/*"
  ],
  "options_page": "options.html",
  "icons": {
    "16": "icons/icon16.png",
    "48": "icons/icon48.png",
    "128": "icons/icon128.png"
  }
}

3. Background Script (background.js)

The background script handles tasks like initialization, authentication, and managing the extension's behavior.

chrome.runtime.onInstalled.addListener(() => {
  console.log("Polite-IT Extension Installed");

  // Authentication logic or API key setup can go here

  // You can listen for messages from the content script or popup
  chrome.runtime.onMessage.addListener((message, sender, sendResponse) => {
    if (message.action === "checkTone") {
      // Handle the message tone check request
      checkMessageTone(message.text).then(response => {
        sendResponse({ analysis: response });
      });
      return true; // Async response
    }
  });
});

// Simulate tone analysis API (This should be replaced with a real API call)
async function checkMessageTone(text) {
  // Dummy response for tone analysis API
  return {
    tone: "friendly",
    suggestions: ["Consider being more formal.", "Use a less casual greeting."]
  };
}

4. Popup UI (popup.html)

This file defines the layout of the extension's popup window.

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Polite-IT</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <h1>Polite-IT</h1>
  <textarea id="messageInput" placeholder="Type your message here..."></textarea>
  <button id="checkToneBtn">Check Tone</button>
  <div id="analysisResult"></div>

  <script src="popup.js"></script>
</body>
</html>

5. Popup JavaScript (popup.js)

This script controls the behavior of the pop-up, including listening for user input and sending it to the background script for tone analysis.

document.getElementById('checkToneBtn').addEventListener('click', function () {
  const messageText = document.getElementById('messageInput').value;

  chrome.runtime.sendMessage({ action: "checkTone", text: messageText }, function (response) {
    displayAnalysis(response.analysis);
  });
});

function displayAnalysis(analysis) {
  const resultDiv = document.getElementById('analysisResult');
  
  if (analysis.tone) {
    resultDiv.innerHTML = `<p><strong>Tone: </strong>${analysis.tone}</p>`;
    resultDiv.innerHTML += `<p><strong>Suggestions:</strong></p><ul>`;
    analysis.suggestions.forEach(suggestion => {
      resultDiv.innerHTML += `<li>${suggestion}</li>`;
    });
    resultDiv.innerHTML += `</ul>`;
  } else {
    resultDiv.innerHTML = '<p>No tone analysis available.</p>';
  }
}

6. Content Script (content.js)

This script will inject the tone analysis feature into Gmail, WhatsApp Web, and Facebook.

// This script will monitor input fields on Gmail, WhatsApp Web, and Facebook for messages

document.addEventListener('input', function (event) {
  if (event.target.matches('.editable-message-field')) {  // Example selector for Gmail message field
    const messageText = event.target.value;

    // Send the message to the background script for tone checking
    chrome.runtime.sendMessage({ action: "checkTone", text: messageText }, function (response) {
      if (response.analysis) {
        // Display tone feedback in a pop-up or inline on the page
        console.log(response.analysis); // Replace with UI feedback implementation
      }
    });
  }
});

7. Tone Analysis API (e.g., Using OpenAI GPT-3)

You can use the OpenAI GPT-3 API or any custom API to analyze the tone of messages. Below is an example of how to interact with an API for tone analysis:

async function getToneAnalysis(text) {
  const response = await fetch('https://api.openai.com/v1/completions', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer YOUR_OPENAI_API_KEY`,
    },
    body: JSON.stringify({
      model: "text-davinci-003",
      prompt: `Analyze the tone of the following message: ${text}`,
      max_tokens: 50
    })
  });

  const data = await response.json();
  return {
    tone: data.choices[0].text.trim(),
    suggestions: ["Consider adjusting your tone to be more professional."]
  };
}

8. Styling (styles.css)

Provide some basic styling for the pop-up and feedback sections.

body {
  font-family: Arial, sans-serif;
  padding: 10px;
}

textarea {
  width: 100%;
  height: 150px;
  margin-bottom: 10px;
  padding: 10px;
}

button {
  background-color: #4CAF50;
  color: white;
  padding: 10px;
  border: none;
  cursor: pointer;
}

button:hover {
  background-color: #45a049;
}

#analysisResult {
  margin-top: 20px;
}

9. Testing and Debugging

Once all the files are implemented, test the extension on Gmail, WhatsApp Web, and Facebook. Check if the tone analysis popup works as expected. Fix any UI bugs and ensure smooth integration with these platforms.
10. Publishing the Extension

Once everything is functional, follow Chrome’s Extension Publishing Guide to submit your extension to the Chrome Web Store.

This is a basic outline for creating the Polite-IT Chrome Extension. You may further enhance features, add sophisticated UI/UX components, or integrate additional APIs to improve message tone analysis. Let me know if you need further assistance or specific additions!
