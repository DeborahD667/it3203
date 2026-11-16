"use strict";

// Text answers omit the choices array. Multiple correct choices use checkboxes.
const questions = [
  ["HTTP stands for ____.", ["Hypertext Transfer Protocol"]],
  ["The protocol that secures HTTPS is abbreviated ____.", ["TLS"]],
  ["Which HTTP method usually retrieves a page?", ["GET"], ["POST", "GET", "DELETE", "PATCH"]],
  ["What does HTTP 404 mean?", ["Not Found"], ["OK", "Not Found", "Forbidden", "Moved Permanently"]],
  ["What does HTTP 200 mean?", ["OK"], ["Not Found", "Bad Request", "OK", "Service Unavailable"]],
  ["Which transport does HTTP/3 use?", ["QUIC"], ["TCP", "FTP", "SMTP", "QUIC"]],
  ["What does HTTP/2 multiplexing allow?", ["Multiple streams on one connection"],
    ["Multiple streams on one connection", "No response codes", "A separate connection for every request", "Browsers to replace servers"]],
  ["Which program usually acts as the client?", ["Web browser"],
    ["Database server", "Web browser", "Web server", "Router"]],
  ["What is the default port for HTTPS?", ["443"], ["21", "25", "80", "443"]],
  ["What does HTTPS help protect? Select all that apply.",
    ["Confidentiality in transit", "Integrity in transit"],
    ["Confidentiality in transit", "Integrity in transit", "Protection from every scam", "Files already stored on a computer"]]
];
const get = id => document.getElementById(id);
const form = get("quiz");
const area = get("questions");
const limit = 600;
const passScore = 7;
let started, deadline, interval;
let active = false;

// Group each question in a fieldset and label every input.
questions.forEach(([prompt, answers, choices], i) => {
  const group = document.createElement("fieldset");
  const legend = document.createElement("legend");
  legend.textContent = `${i + 1}. ${prompt}`;
  group.append(legend);
  (choices || ["Your answer"]).forEach(choice => {
    const label = document.createElement("label");
    const input = document.createElement("input");
    input.name = `q${i + 1}`;
    input.type = !choices ? "text" : answers.length === 1 ? "radio" : "checkbox";
    if (choices) {
      input.value = choice;
      label.append(input, ` ${choice}`);
    } else {
      input.autocomplete = "off";
      label.append(choice, document.createElement("br"), input);
    }
    group.append(label);
  });
  area.append(group);
});

function formatTime(seconds) {
  return `${Math.floor(seconds / 60)}:${String(seconds % 60).padStart(2, "0")}`;
}

function remaining() {
  return Math.max(0, Math.ceil((deadline - Date.now()) / 1000));
}

// Use a deadline instead of counting interval ticks, which can be delayed.
function tick() {
  const seconds = remaining();
  get("timer").textContent = formatTime(seconds);
  if (seconds === 0) finish();
}

function start() {
  clearInterval(interval);
  form.reset();
  get("feedback").replaceChildren();
  get("score").textContent = "";
  get("elapsed").textContent = "";
  get("results").hidden = true;
  get("start").hidden = true;
  form.hidden = false;
  started = Date.now();
  deadline = started + limit * 1000;
  active = true;
  tick();
  interval = setInterval(tick, 250);
  area.querySelector("input").focus();
}

const normalize = value => value.trim().toLowerCase().replace(/\s+/g, " ");

function finish() {
  if (!active) return;
  active = false;
  clearInterval(interval);
  const now = Date.now();
  const elapsed = Math.min(limit, Math.floor((now - started) / 1000));
  const responses = new FormData(form);
  let score = 0;

  // Select-all questions require every correct choice and no extra choices.
  questions.forEach(([prompt, answers], i) => {
    const entered = responses.getAll(`q${i + 1}`).map(value => value.trim()).filter(Boolean);
    const values = entered.map(normalize);
    const correct = values.length === answers.length &&
      answers.every(answer => values.includes(normalize(answer)));
    if (correct) score++;
    const item = document.createElement("li");
    const result = document.createElement("strong");
    result.className = correct ? "quiz-correct" : "quiz-incorrect";
    result.textContent = correct ? "Correct (1/1)" : "Incorrect (0/1)";
    item.append(`${prompt} — `, result,
      ` | Your answer: ${entered.join("; ") || "No answer"} | Correct answer: ${answers.join("; ")}`);
    get("feedback").append(item);
  });

  const passed = score >= passScore;
  get("score").className = passed ? "quiz-correct" : "quiz-incorrect";
  get("score").textContent = `${passed ? "Pass" : "Fail"} — ${score}/${questions.length}`;
  get("elapsed").textContent = `${now >= deadline ? "Time expired. " : ""}Time spent: ${formatTime(elapsed)}`;
  get("timer").textContent = formatTime(remaining());
  form.hidden = true;
  get("results").hidden = false;
  get("result-title").focus();
}

["start", "retake", "try-again"].forEach(id => get(id).addEventListener("click", start));
form.addEventListener("submit", event => {
  event.preventDefault();
  finish();
});
