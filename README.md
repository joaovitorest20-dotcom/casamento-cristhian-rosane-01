<script>
document.addEventListener('DOMContentLoaded', () => {
  // 1. Envelope e rolagem suave
  const envelope = document.getElementById('envelope');
  const openBtn = document.getElementById('openInvite');
  const hint = document.getElementById('hint');
  const mensagemSection = document.getElementById('mensagem');

  function abrirConvite() {
    if (!envelope) return;
    envelope.classList.add('open');
    if (openBtn) openBtn.textContent = 'Convite aberto';
    if (hint) hint.textContent = 'COM CARINHO, CRISTHIAN & ROSANE';

    if (mensagemSection) {
      setTimeout(() => {
        mensagemSection.scrollIntoView({ behavior: 'smooth', block: 'start' });
      }, 650);
    }
  }

  openBtn?.addEventListener('click', abrirConvite);
  envelope?.addEventListener('click', abrirConvite);
  envelope?.addEventListener('keydown', (e) => {
    if (e.key === 'Enter' || e.key === ' ') {
      e.preventDefault();
      abrirConvite();
    }
  });

  // 2. Contagem regressiva (23/01/2027 às 18h30)
  const weddingDate = new Date('2027-01-23T18:30:00-03:00').getTime();
  const els = {
    days: document.getElementById('days'),
    hours: document.getElementById('hours'),
    minutes: document.getElementById('minutes'),
    seconds: document.getElementById('seconds')
  };

  function updateCountdown() {
    const diff = Math.max(0, weddingDate - Date.now());
    const vals = {
      days: Math.floor(diff / 86400000),
      hours: Math.floor((diff % 86400000) / 3600000),
      minutes: Math.floor((diff % 3600000) / 60000),
      seconds: Math.floor((diff % 60000) / 1000)
    };

    Object.entries(vals).forEach(([key, val]) => {
      if (els[key]) {
        els[key].textContent = String(val).padStart(2, '0');
      }
    });
  }

  updateCountdown();
  setInterval(updateCountdown, 1000);

  // 3. Cópia segura compatível com celulares e webviews
  function fallbackCopy(text) {
    return new Promise((resolve, reject) => {
      const field = document.createElement('textarea');
      field.value = text;
      field.setAttribute('readonly', '');
      field.style.position = 'fixed';
      field.style.top = '0';
      field.style.left = '0';
      field.style.width = '2em';
      field.style.height = '2em';
      field.style.opacity = '0.01';
      field.style.fontSize = '16px';
      document.body.appendChild(field);
      field.focus();
      field.select();
      field.setSelectionRange(0, text.length);
      const ok = document.execCommand('copy');
      document.body.removeChild(field);
      ok ? resolve() : reject(new Error('Falha ao copiar'));
    });
  }

  function copyTextSafe(text) {
    if (navigator.clipboard && window.isSecureContext) {
      return navigator.clipboard.writeText(text).catch(() => fallbackCopy(text));
    }
    return fallbackCopy(text);
  }

  document.querySelectorAll('[data-copy]').forEach((btn) => {
    btn.addEventListener('click', async () => {
      const value = btn.dataset.copy;
      const original = btn.textContent;
      try {
        await copyTextSafe(value);
        btn.textContent = '✓ Chave copiada!';
        btn.classList.add('copied');
        setTimeout(() => {
          btn.textContent = original;
          btn.classList.remove('copied');
        }, 2200);
      } catch (e) {
        btn.textContent = 'Selecione e copie acima';
        setTimeout(() => {
          btn.textContent = original;
        }, 2200);
      }
    });
  });

  // 4. Confirmação de presença via WhatsApp
  const whatsappNumber = '554599201233';
  const rsvpBtn = document.getElementById('rsvpBtn');
  const guestInput = document.getElementById('guestName');
  const feedback = document.getElementById('feedback');

  async function enviarConfirmacao() {
    const name = guestInput ? guestInput.value.trim() : '';
    if (!name) {
      if (feedback) feedback.textContent = 'Por favor, digite seu nome e sobrenome acima.';
      guestInput?.focus();
      return;
    }

    const message = `Olá, Cristhian e Rosane! Aqui é ${name}. Gostaria de confirmar minha presença no casamento de vocês, no dia 23/01/2027!`;
    const waUrl = `https://wa.me/${whatsappNumber}?text=${encodeURIComponent(message)}`;

    try {
      await copyTextSafe(message);
    } catch (e) {}

    if (feedback) {
      feedback.textContent = 'Abrindo o WhatsApp dos noivos com sua mensagem pronta...';
    }
    window.open(waUrl, '_blank', 'noopener,noreferrer');
  }

  rsvpBtn?.addEventListener('click', enviarConfirmacao);
  guestInput?.addEventListener('keydown', (e) => {
    if (e.key === 'Enter') {
      e.preventDefault();
      enviarConfirmacao();
    }
  });
});
</script>

