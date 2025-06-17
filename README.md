// Recuperar dados do jogador (moedas e roupas compradas) do localStorage
let moedas = parseInt(localStorage.getItem("moedas")) || 0;
let roupasCompradas = JSON.parse(localStorage.getItem("roupasCompradas")) || [];

// Lista de roupas disponíveis na loja
const roupasDisponiveis = [
  { nome: "Vestido Rosa", arquivo: "vestido-rosa.png", preco: 30 },
  { nome: "Vestido Azul", arquivo: "vestido-azul.png", preco: 40 },
];

// Salvar moedas e roupas compradas no localStorage
function salvarDados() {
  localStorage.setItem("moedas", moedas);
  localStorage.setItem("roupasCompradas", JSON.stringify(roupasCompradas));
}

// Atualizar o contador de moedas na tela
function atualizarMoedas() {
  document.getElementById("coin-count").textContent = moedas;
  salvarDados();
}

// Trocar de tela (menu, loja, quarto de vestir, etc)
function showScreen(screenId) {
  const screens = document.querySelectorAll(".screen");
  screens.forEach(screen => screen.classList.add("hidden"));
  document.getElementById(screenId).classList.remove("hidden");

  atualizarMoedas();

  if (screenId === "shop") {
    exibirLoja();
  }

  if (screenId === "dressRoom") {
    exibirCloset();
  }
}

// Trocar a roupa da boneca
function trocarRoupa(arquivoImagem) {
  const roupa = document.getElementById("clothing");
  if (arquivoImagem) {
    roupa.src = "img/" + arquivoImagem;
    roupa.style.display = "block";
  } else {
    roupa.style.display = "none";
  }
}

// Trabalho: Juiz de Moda
function startJudgeJob() {
  const workResults = document.getElementById("work-results");
  const recompensa = 50;

  moedas += recompensa;

  workResults.innerHTML = `
    <p>Você avaliou 3 bonecas com looks incríveis!</p>
    <p>⭐ ⭐ ⭐ ⭐ ⭐</p>
    <p>Você ganhou <strong>${recompensa}</strong> moedas!</p>
  `;

  atualizarMoedas();
}

// Exibir itens da loja de roupas
function exibirLoja() {
  const loja = document.getElementById("shop-items");
  const msg = document.getElementById("shop-message");
  loja.innerHTML = "";
  msg.textContent = "";

  roupasDisponiveis.forEach(roupa => {
    const btn = document.createElement("button");
    const jaComprou = roupasCompradas.includes(roupa.arquivo);

    btn.textContent = jaComprou
      ? `${roupa.nome} ✅`
      : `${roupa.nome} - ${roupa.preco} moedas`;

    btn.disabled = jaComprou;

    btn.onclick = () => {
      if (moedas >= roupa.preco) {
        moedas -= roupa.preco;
        roupasCompradas.push(roupa.arquivo);
        atualizarMoedas();
        salvarDados();
        exibirLoja();
        msg.textContent = `Você comprou ${roupa.nome}!`;
      } else {
        msg.textContent = "Você não tem moedas suficientes.";
      }
    };

    loja.appendChild(btn);
  });
}

// Exibir o closet com as roupas compradas
function exibirCloset() {
  const closet = document.getElementById("closet");
  closet.innerHTML = "";

  if (roupasCompradas.length === 0) {
    closet.innerHTML = "<p>Você ainda não comprou roupas!</p>";
    return;
  }

  roupasCompradas.forEach(arquivo => {
    const btn = document.createElement("button");
    btn.textContent = arquivo.replace(".png", "").replace("-", " ").toUpperCase();
    btn.onclick = () => trocarRoupa(arquivo);
    closet.appendChild(btn);
  });

  const btnNenhuma = document.createElement("button");
  btnNenhuma.textContent = "Sem Roupa";
  btnNenhuma.onclick = () => trocarRoupa("");
  closet.appendChild(btnNenhuma);
}
