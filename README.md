# dbf.projeto.01
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DBF - Painel Interativo</title>
    <style>
        /* Estilos CSS (manter a maioria como antes, com algumas adições) */
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f4f4f4;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }

        .painel {
            background-color: #fff;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            border-radius: 8px;
            width: 90%;
            max-width: 1000px;
            padding: 20px;
            text-align: center;
        }

        .header {
            background-color: #004d99; /* Azul escuro DBF */
            color: white;
            padding: 15px;
            border-radius: 6px 6px 0 0;
            margin: -20px -20px 20px -20px;
            font-size: 24px;
            font-weight: bold;
        }

        /* Estilos do Calendário */
        .calendario-container {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            gap: 5px;
            margin-bottom: 20px;
        }

        .dia-semana {
            background-color: #e0e0e0;
            padding: 10px;
            font-weight: bold;
            border-radius: 4px;
        }

        .dia-bloco {
            background-color: #f9f9f9;
            border: 1px solid #ddd;
            padding: 10px 5px;
            height: 80px;
            cursor: pointer;
            border-radius: 4px;
            display: flex;
            flex-direction: column;
            align-items: flex-start;
            text-align: left;
            transition: background-color 0.2s;
            position: relative;
        }

        .dia-bloco:hover {
            background-color: #e6f7ff; /* Cor de hover suave */
        }

        .dia-bloco.hoje {
            border: 2px solid #ff9900; /* Borda laranja para o dia atual */
        }

        .numero-dia {
            font-size: 1.2em;
            font-weight: bold;
            margin-bottom: 5px;
            color: #333;
        }
        
        .nota-preview {
            font-size: 0.7em;
            color: #666;
            overflow: hidden;
            text-overflow: ellipsis;
            display: -webkit-box;
            -webkit-line-clamp: 2; /* Limita a 2 linhas */
            -webkit-box-orient: vertical;
        }
        
        /* Ícones de indicação de conteúdo */
        .dia-bloco.tem-nota::before {
            content: '📝';
            position: absolute;
            top: 5px;
            right: 5px;
            font-size: 0.8em;
        }

        .dia-bloco.tem-imagem::after {
            content: '🖼️';
            position: absolute;
            bottom: 5px;
            right: 5px;
            font-size: 0.8em;
        }

        /* Estilos do Navegador de Meses */
        .navegacao {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
        }

        .navegacao button {
            background-color: #007bff;
            color: white;
            border: none;
            padding: 10px 15px;
            cursor: pointer;
            border-radius: 4px;
            font-size: 16px;
        }

        .mes-atual {
            font-size: 1.5em;
            font-weight: bold;
            color: #004d99;
        }

        /* Estilos do Modal (Bloco de Notas) */
        .modal {
            display: none;
            position: fixed;
            z-index: 100; /* Garante que fique acima de tudo */
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            overflow: auto;
            background-color: rgba(0, 0, 0, 0.4);
            backdrop-filter: blur(2px); /* Efeito de desfoque */
        }

        .modal-conteudo {
            background-color: #fefefe;
            margin: 10% auto; /* Ajustado para um pouco mais para cima */
            padding: 20px;
            border: 1px solid #888;
            width: 90%;
            max-width: 600px; /* Um pouco maior para a imagem */
            border-radius: 8px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
            position: relative; /* Para o close button */
        }

        .fechar-modal {
            color: #aaa;
            float: right;
            font-size: 28px;
            font-weight: bold;
            position: absolute;
            top: 10px;
            right: 20px;
            cursor: pointer;
        }

        .fechar-modal:hover,
        .fechar-modal:focus {
            color: black;
            text-decoration: none;
        }
        
        #nota-textarea {
            width: 100%;
            height: 150px; /* Menor para dar espaço à imagem */
            padding: 10px;
            margin-top: 10px;
            box-sizing: border-box;
            border: 1px solid #ccc;
            border-radius: 4px;
            resize: vertical;
            margin-bottom: 15px; /* Espaço para o input de arquivo */
        }
        
        #input-imagem {
            margin-bottom: 10px;
            display: block; /* Para ocupar a largura total */
            width: 100%;
            box-sizing: border-box;
        }

        .previa-imagem {
            max-width: 100%;
            max-height: 200px; /* Altura máxima para a prévia */
            margin-top: 10px;
            display: block;
            border: 1px solid #ddd;
            border-radius: 4px;
            object-fit: contain; /* Garante que a imagem inteira seja visível */
        }
        
        .botoes-modal {
            display: flex;
            justify-content: flex-end; /* Alinha os botões à direita */
            gap: 10px; /* Espaçamento entre botões */
            margin-top: 15px;
        }

        #salvar-nota {
            background-color: #28a745;
            color: white;
            border: none;
            padding: 10px 15px;
            cursor: pointer;
            border-radius: 4px;
            font-size: 16px;
        }

        #remover-imagem {
            background-color: #dc3545;
            color: white;
            border: none;
            padding: 10px 15px;
            cursor: pointer;
            border-radius: 4px;
            font-size: 16px;
            display: none; /* Escondido por padrão */
        }
    </style>
</head>
<body>

<div class="painel">
    <div class="header">DBF - Painel Interativo</div>

    <div class="navegacao">
        <button id="anterior">← Anterior</button>
        <div class="mes-atual" id="mes-atual">Mês/Ano</div>
        <button id="proximo">Próximo →</button>
    </div>

    <div class="calendario-container" id="calendario-container">
        <div class="dia-semana">Dom</div>
        <div class="dia-semana">Seg</div>
        <div class="dia-semana">Ter</div>
        <div class="dia-semana">Qua</div>
        <div class="dia-semana">Qui</div>
        <div class="dia-semana">Sex</div>
        <div class="dia-semana">Sáb</div>
    </div>
</div>

<div id="nota-modal" class="modal">
    <div class="modal-conteudo">
        <span class="fechar-modal">&times;</span>
        <h3 id="modal-titulo">Bloco de Notas - Data</h3>
        
        <textarea id="nota-textarea" placeholder="Digite sua nota aqui..."></textarea>
        
        <label for="input-imagem">Anexar Imagem:</label>
        <input type="file" id="input-imagem" accept="image/*">
        
        <img id="previa-imagem" class="previa-imagem" src="" alt="Prévia da Imagem" style="display: none;">
        
        <div class="botoes-modal">
            <button id="remover-imagem">Remover Imagem</button>
            <button id="salvar-nota">Salvar Nota</button>
        </div>
    </div>
</div>

<script>
    // Código JavaScript
    document.addEventListener('DOMContentLoaded', () => {
        const calendarioContainer = document.getElementById('calendario-container');
        const mesAtualEl = document.getElementById('mes-atual');
        const btnAnterior = document.getElementById('anterior');
        const btnProximo = document.getElementById('proximo');
        const notaModal = document.getElementById('nota-modal');
        const fecharModal = document.querySelector('.fechar-modal');
        const modalTitulo = document.getElementById('modal-titulo');
        const notaTextarea = document.getElementById('nota-textarea');
        const inputImagem = document.getElementById('input-imagem');
        const previaImagem = document.getElementById('previa-imagem');
        const salvarNotaBtn = document.getElementById('salvar-nota');
        const removerImagemBtn = document.getElementById('remover-imagem');

        let dataAtual = new Date(); // Inicia com a data de hoje
        let dataSelecionada = null; // Armazena a data do bloco clicado
        let imagemBase64 = null; // Armazena a imagem em Base64 para a nota atual

        // --- Funções Principais ---

        // Função para obter as notas salvas no localStorage
        function obterNotas() {
            const notasJSON = localStorage.getItem('dbfNotas');
            return notasJSON ? JSON.parse(notasJSON) : {};
        }

        // Função para salvar as notas no localStorage
        function salvarNotas(notas) {
            localStorage.setItem('dbfNotas', JSON.stringify(notas));
        }

        // Função para formatar a data como chave (ex: 2025-11-01)
        function formatarDataChave(data) {
            const ano = data.getFullYear();
            const mes = String(data.getMonth() + 1).padStart(2, '0');
            const dia = String(data.getDate()).padStart(2, '0');
            return `${ano}-${mes}-${dia}`;
        }

        // Função para renderizar o calendário do mês/ano atual
        function renderizarCalendario(data) {
            // Remove apenas os blocos de dias, mantendo os dias da semana
            const diasParaRemover = calendarioContainer.querySelectorAll('.dia-bloco');
            diasParaRemover.forEach(bloco => bloco.remove());

            const ano = data.getFullYear();
            const mes = data.getMonth(); // 0 a 11

            // Define o nome do mês
            const nomeMes = data.toLocaleString('pt-BR', { month: 'long', year: 'numeric' });
            mesAtualEl.textContent = nomeMes.charAt(0).toUpperCase() + nomeMes.slice(1);

            // Obtém o primeiro dia do mês (para saber em qual dia da semana começar)
            const primeiroDia = new Date(ano, mes, 1);
            const diaSemanaPrimeiro = primeiroDia.getDay(); // 0 (Dom) a 6 (Sáb)

            // Obtém o último dia do mês
            const ultimoDia = new Date(ano, mes + 1, 0).getDate();

            // Notas salvas
            const notasSalvas = obterNotas();
            const hojeChave = formatarDataChave(new Date());

            // Adiciona blocos vazios para preencher até o primeiro dia da semana
            for (let i = 0; i < diaSemanaPrimeiro; i++) {
                const blocoVazio = document.createElement('div');
                blocoVazio.classList.add('dia-bloco', 'vazio');
                calendarioContainer.appendChild(blocoVazio);
            }

            // Adiciona os blocos dos dias do mês
            for (let dia = 1; dia <= ultimoDia; dia++) {
                const diaBloco = document.createElement('div');
                diaBloco.classList.add('dia-bloco');
                diaBloco.dataset.dia = dia;

                const dataBloco = new Date(ano, mes, dia);
                const chaveData = formatarDataChave(dataBloco);

                // Marca o dia de hoje
                if (chaveData === hojeChave) {
                    diaBloco.classList.add('hoje');
                }

                // Conteúdo do bloco
                diaBloco.innerHTML = `<div class="numero-dia">${dia}</div>`;

                // Prepara e exibe a pré-visualização da nota e/ou imagem, se existirem
                if (notasSalvas[chaveData]) {
                    const { texto, imagem } = notasSalvas[chaveData];

                    if (texto && texto.trim() !== '') {
                        diaBloco.classList.add('tem-nota');
                        const notaPreview = document.createElement('div');
                        notaPreview.classList.add('nota-preview');
                        notaPreview.textContent = texto;
                        diaBloco.appendChild(notaPreview);
                    }
                    if (imagem) {
                        diaBloco.classList.add('tem-imagem');
                        // Poderíamos adicionar uma prévia menor da imagem aqui, mas os ícones já dão a dica
                    }
                }

                // Evento de clique para abrir o bloco de notas
                diaBloco.addEventListener('click', () => abrirBlocoNotas(dataBloco));

                calendarioContainer.appendChild(diaBloco);
            }
        }

        // Função para abrir o bloco de notas (Modal)
        function abrirBlocoNotas(data) {
            dataSelecionada = data;
            const chaveData = formatarDataChave(data);
            const notas = obterNotas();
            const { texto = '', imagem = null } = notas[chaveData] || {}; // Desestrutura para texto e imagem

            // Reseta o input de arquivo e a prévia da imagem
            inputImagem.value = ''; // Limpa o arquivo selecionado
            previaImagem.style.display = 'none';
            previaImagem.src = '';
            removerImagemBtn.style.display = 'none'; // Esconde o botão remover

            modalTitulo.textContent = `Bloco de Notas - ${data.toLocaleDateString('pt-BR', { day: '2-digit', month: 'long', year: 'numeric' })}`;
            notaTextarea.value = texto;
            imagemBase64 = imagem; // Carrega a imagem existente para a variável

            if (imagemBase64) {
                previaImagem.src = imagemBase64;
                previaImagem.style.display = 'block';
                removerImagemBtn.style.display = 'inline-block'; // Mostra o botão remover
            }
            
            notaModal.style.display = 'block';
        }

        // Função para converter arquivo de imagem para Base64
        function converterImagemParaBase64(arquivo, callback) {
            const reader = new FileReader();
            reader.onload = function(event) {
                callback(event.target.result);
            };
            reader.onerror = function(error) {
                console.error("Erro ao ler o arquivo:", error);
                callback(null);
            };
            reader.readAsDataURL(arquivo);
        }

        // Função para salvar a nota (chamada pelo botão do modal)
        async function salvarNota() {
            if (!dataSelecionada) return;

            const chaveData = formatarDataChave(dataSelecionada);
            const texto = notaTextarea.value.trim();
            const notas = obterNotas();

            let imagemParaSalvar = imagemBase64; // Inicia com a imagem que já estava ou foi carregada

            // Se um novo arquivo foi selecionado, converte e usa
            if (inputImagem.files.length > 0) {
                await new Promise(resolve => {
                    converterImagemParaBase64(inputImagem.files[0], (result) => {
                        imagemParaSalvar = result;
                        resolve();
                    });
                });
            }

            // Só salva se tiver texto OU imagem
            if (texto === '' && !imagemParaSalvar) {
                delete notas[chaveData]; // Remove a entrada se não tiver nada
            } else {
                notas[chaveData] = { texto: texto, imagem: imagemParaSalvar };
            }

            salvarNotas(notas);
            notaModal.style.display = 'none'; // Fecha o modal
            renderizarCalendario(dataAtual); // Atualiza o calendário para refletir a mudança
            imagemBase64 = null; // Reseta após salvar
        }
        
        // --- Event Listeners ---

        // Botão para Mês Anterior
        btnAnterior.addEventListener('click', () => {
            dataAtual.setMonth(dataAtual.getMonth() - 1);
            renderizarCalendario(dataAtual);
        });

        // Botão para Próximo Mês
        btnProximo.addEventListener('click', () => {
            dataAtual.setMonth(dataAtual.getMonth() + 1);
            renderizarCalendario(dataAtual);
        });

        // Fechar modal ao clicar no 'x'
        fecharModal.addEventListener('click', () => {
            notaModal.style.display = 'none';
            inputImagem.value = ''; // Limpa o input de arquivo ao fechar
            previaImagem.style.display = 'none';
            previaImagem.src = '';
            removerImagemBtn.style.display = 'none';
            imagemBase64 = null; // Reseta a imagemBase64 também
        });

        // Fechar modal ao clicar fora dele
        window.addEventListener('click', (event) => {
            if (event.target === notaModal) {
                notaModal.style.display = 'none';
                inputImagem.value = ''; // Limpa o input de arquivo ao fechar
                previaImagem.style.display = 'none';
                previaImagem.src = '';
                removerImagemBtn.style.display = 'none';
                imagemBase64 = null; // Reseta a imagemBase64 também
            }
        });
        
        // Pré-visualização da imagem ao selecionar um arquivo
        inputImagem.addEventListener('change', (event) => {
            const arquivo = event.target.files[0];
            if (arquivo) {
                converterImagemParaBase64(arquivo, (result) => {
                    previaImagem.src = result;
                    previaImagem.style.display = 'block';
                    removerImagemBtn.style.display = 'inline-block';
                    // Nota: A imagemBase64 será atualizada no salvarNota, 
                    // a não ser que queiramos que ela seja atualizada imediatamente aqui.
                    // Para o caso de o usuário selecionar e depois mudar de ideia sem salvar, 
                    // é melhor deixar para atualizar imagemBase64 no salvarNota.
                });
            } else {
                previaImagem.style.display = 'none';
                previaImagem.src = '';
                removerImagemBtn.style.display = 'none';
            }
        });

        // Botão para remover a imagem atual
        removerImagemBtn.addEventListener('click', () => {
            inputImagem.value = ''; // Limpa o input de arquivo
            previaImagem.style.display = 'none';
            previaImagem.src = '';
            removerImagemBtn.style.display = 'none';
            imagemBase64 = null; // Limpa a imagem para salvar
        });

        // Botão Salvar Nota
        salvarNotaBtn.addEventListener('click', salvarNota);

        // --- Inicialização ---
        renderizarCalendario(dataAtual);
    });
</script>

</body>
</html>
