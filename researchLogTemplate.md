<% "---" %>
aliases:
tags:
  - researchLog
date: <% tp.date.now("YYYY-MM-DD") %>
year: <% tp.date.now("YYYY") %>
month: <% tp.date.now("MM") %>
week: <% tp.date.now("WW") %>
<% "---" %>

<%*
// --- 1. Build a base file name ---
const prefix = "research log ";
const todayStr = tp.date.now("YYYY-MM-DD");
let baseName  = prefix + todayStr;

// --- 2. Check if file exists ---
async function fileNameExists(name) {
  // Templater's "tp.file.exists" checks if name + ".md" is in your vault
  return await tp.file.exists(name + ".md");
}

// --- 3. Increment a counter until we have a unique name ---
let attempt = 0;
let candidateName = baseName;

while (await fileNameExists(candidateName)) {
  attempt++;
  candidateName = `${baseName} (${attempt})`;
}

// --- 4. Perform the rename with the new name ---
await tp.file.rename(candidateName);
%>
# What did I do & how did I do it?


# Why did I do this?


# What were the results?


# Interpretation / learning? 


# Next steps / open questions

