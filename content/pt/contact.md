---
title: "Contato"
---

Entre em contato conosco.

📱 **WhatsApp:** [Clique aqui para entrar no nosso grupo](https://chat.whatsapp.com/5kV0suS9TLBIyrp5StJJFi?mode=gi_t)

---

<form id="contact-form">
  <label for="name">Nome:</label>
  <input type="text" id="name" name="name" required><br>

  <label for="email">Email:</label>
  <input type="email" id="email" name="email" required><br>

  <label for="message">Mensagem:</label>
  <textarea id="message" name="message" required></textarea><br>

  <button type="submit">Enviar</button>
</form>

<script>
document.getElementById('contact-form').addEventListener('submit', function(e) {
  e.preventDefault();
  // Code to send to Lambda API
  alert('Form submitted!'); // Placeholder
});
</script>