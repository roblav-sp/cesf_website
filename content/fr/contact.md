---
title: "Contact"
---

Contactez-nous.

<form id="contact-form">
  <label for="name">Nom:</label>
  <input type="text" id="name" name="name" required><br>

  <label for="email">Email:</label>
  <input type="email" id="email" name="email" required><br>

  <label for="message">Message:</label>
  <textarea id="message" name="message" required></textarea><br>

  <button type="submit">Envoyer</button>
</form>

<script>
document.getElementById('contact-form').addEventListener('submit', function(e) {
  e.preventDefault();
  // Code to send to Lambda API
  alert('Form submitted!'); // Placeholder
});
</script>