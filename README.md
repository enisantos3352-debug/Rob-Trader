<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simulador Robô BTC - Média Longa</title>
    <style>
        body { background-color: #121212; color: #ffffff; font-family: sans-serif; display: flex; flex-direction: column; justify-content: center; align-items: center; min-height: 100vh; margin: 0; }
        .card { background-color: #1e1e1e; border-radius: 16px; padding: 25px; width: 90%; max-width: 350px; box-shadow: 0 8px 25px rgba(0,0,0,0.6); text-align: center; border: 1px solid #333; }
        h1 { color: #fbc02d; font-size: 22px; margin-bottom: 8px; }
        p.sub { color: #9e9e9e; font-size: 13px; margin-bottom: 20px; }
        .box { background-color: #2c2c2c; border-radius: 12px; padding: 15px; margin-bottom: 12px; }
        .box-title { color: #bdbdbd; font-size: 12px; margin-bottom: 5px; text-transform: uppercase; letter-spacing: 0.5px; }
        .box-value { font-size: 22px; font-weight: bold; }
        #preco-val { color: #4caf50; }
        #ma-val { color: #fbc02d; }
        #sinal-val { font-size: 16px; text-transform: uppercase; }
    </style>
</head>
<body>

<div class="card">
    <h1>Simulador Robô BTC</h1>
    <p class="sub">Estratégia de Média Móvel Longa (Tempo Real)</p>
    
    <div class="box">
        <div class="box-title">Preço Atual do BTC</div>
        <div class="box-value" id="preco-val">Conectando...</div>
    </div>

    <div class="box">
        <div class="box-title">Média Móvel Longa (MA)</div>
        <div class="box-value" id="ma-val">Calculando...</div>
    </div>

    <div class="box" id="sinal-box" style="background-color: #333;">
        <div class="box-title">Sinal do Robô</div>
        <div class="box-value" id="sinal-val">Aguardando dados...</div>
    </div><script>
    let precosHistorico = [];
    const periodoLongo = 30; // Aumentado para a média ficar bem longa e distante

    async function buscarPrecoBTC() {
        try {
            let resposta = await fetch('https://api.binance.com/api/v3/ticker/price?symbol=BTCUSDT');
            let dados = await resposta.json();
            let precoAtual = parseFloat(dados.price);

            document.getElementById('preco-val').innerText = '$ ' + precoAtual.toLocaleString('en-US', {minimumFractionDigits: 2});

            precosHistorico.push(precoAtual);
            if (precosHistorico.length > 60) {
                precosHistorico.shift();
            }

            if (precosHistorico.length >= periodoLongo) {
                let soma = 0;
                for (let i = 0; i < periodoLongo; i++) {
                    soma += precosHistorico[precosHistorico.length - 1 - i];
                }
                let mediaMovel = soma / periodoLongo;
                document.getElementById('ma-val').innerText = '$ ' + mediaMovel.toLocaleString('en-US', {minimumFractionDigits: 2});

                let boxSinal = document.getElementById('sinal-box');
                let txtSinal = document.getElementById('sinal-val');

                if (precoAtual > mediaMovel) {
                    txtSinal.innerText = "▲ COMPRA (Tendência Alta)";
                    txtSinal.style.color = "#4caf50";
                    boxSinal.style.backgroundColor = "#1b381b";
                } else {
                    txtSinal.innerText = "▼ VENDA (Tendência Baixa)";
                    txtSinal.style.color = "#f44336";
                    boxSinal.style.backgroundColor = "#381b1b";
                }
            } else {
                document.getElementById('ma-val').innerText = "Acumulando dados (" + precosHistorico.length + "/" + periodoLongo + ")...";
            }

        } catch (erro) {
            document.getElementById('preco-val').innerText = "Erro na Conexão";
        }
    }

    // Atualiza a cada 2 segundos
    setInterval(buscarPrecoBTC, 2000);
    buscarPrecoBTC();
</script>

</body>
</html>

</div>
