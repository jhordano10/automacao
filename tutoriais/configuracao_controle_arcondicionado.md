
# 💡 Integração de Controles Infravermelhos Tuya no Home Assistant

Este guia apresenta o passo a passo necessário para integrar controles remotos infravermelhos da **Tuya** (como hubs IR universais) ao **Home Assistant**, utilizando a funcionalidade de **termostato genérico** e **automação com cenas**.

⚠️ **Atenção:** A integração oficial da Tuya com o Home Assistant **não suporta diretamente** os hubs IR e seus controles remotos. Por isso, será utilizada uma abordagem alternativa, baseada em cenas e automações.

---

## 🛠️ Requisitos

- Hub IR Tuya configurado no app **Tuya Smart** ou **Smart Life**
- Dispositivo configurado no hub (ex: ar-condicionado)
- Home Assistant instalado e funcional
- Conhecimento básico em edição do `configuration.yaml`

---

## 📋 Etapas de Configuração

### 1️⃣ Configurar o hub IR no app Tuya ou Smart Life  
Siga o assistente do aplicativo para adicionar o hub IR universal normalmente.

### 2️⃣ Configurar o controle remoto no app  
Adicione um novo controle remoto (por exemplo, de **ar-condicionado**) e faça os testes de funcionamento via app.

### 3️⃣ Criar cenas no app Tuya/Smart Life  
Crie **cenas do tipo "Tocar para executar"** para as principais funções desejadas, como:
- Ligar o ar-condicionado (modo refrigerar, 23°C, ventilação automática)
- Desligar o ar-condicionado

### 4️⃣ Configurar a integração Tuya no Home Assistant  
No Home Assistant:
- Vá em **Configurações > Dispositivos e Serviços**
- Clique em **Adicionar Integração**
- Busque por **Tuya**, faça login com sua conta e autorize

---

## ⚙️ Configuração no Home Assistant

### 5️⃣ Adicionar o termostato genérico  
No arquivo `configuration.yaml`, adicione:

```yaml
# Termostato genérico para controle de ar-condicionado via Tuya
climate:
  - platform: generic_thermostat
    unique_id: GTerm
    name: termostato1
    heater: input_boolean.ar_ligado
    target_sensor: sensor.teste
    ac_mode: true
    min_temp: 24
    target_temp: 26
    hot_tolerance: 0
    precision: 1
```

> Substitua `sensor.teste` por um sensor de temperatura real, se disponível.

---

### 6️⃣ Validar e reiniciar o Home Assistant  
- Vá em **Ferramentas de Desenvolvedor > Verificar configuração**
- Se tudo estiver correto, reinicie o Home Assistant

---

### 7️⃣ Criar Input Boolean  
- Vá em **Configurações > Dispositivos e Serviços > Ajudantes (Helpers)**
- Clique em **Criar Ajudante** > **Alternância (Input Boolean)**
- Nomeie como `ar_ligado`, escolha um ícone (opcional)

---

## 🔁 Automatizando as ações

### 8️⃣ Automação: Ligar o ar-condicionado

1. Vá em **Configurações > Automações e Cenas > Criar Automação**
2. Trigger 1:  
   - Tipo: **Estado**
   - Entidade: `climate.termostato1`
3. Trigger 2:  
   - Tipo: **Estado**
   - Entidade: `climate.termostato1`
   - Atributo: `temperatura alvo`
4. Ação:
   - Tipo: **Condição**
   - Template:  
     ```jinja2
     {{ states('climate.termostato1') == 'cool' }}
     ```
   - Depois:
     - Tipo: **Ativar cena**
     - Selecione a cena Tuya para **ligar o ar-condicionado**

---

### 9️⃣ Automação: Desligar o ar-condicionado

1. Criar nova automação
2. Trigger:
   - Tipo: **Estado**
   - Entidade: `climate.termostato1`
3. Condição:
   - Tipo: **Template**
   - Valor:
     ```jinja2
     {{ states('climate.termostato1') == 'off' }}
     ```
4. Ações:
   - Ativar cena: selecione a cena Tuya para **desligar o ar-condicionado**
   - Alterar estado do input boolean: selecione `ar_ligado` e defina como **desligado**

---

### 🔄 Reinicie novamente (opcional)

- Vá em **Ferramentas de Desenvolvedor > Verificar configuração**
- Reinicie o Home Assistant se necessário

---

## ✅ Resultado Final

Você terá um **termostato virtual** funcional no Home Assistant que:
- Liga o ar-condicionado com base na cena Tuya
- Desliga com outra cena
- Pode ser controlado via dashboard do Home Assistant, assistentes de voz ou automações adicionais

---

## 📌 Observações

- As cenas devem ser testadas no aplicativo antes de integrar.
- Você pode expandir esse modelo para diferentes modos (ex: aquecer, desumidificar), criando novas cenas e automações específicas.

---

## 🤝 Contribuições

Sinta-se à vontade para abrir issues ou pull requests com melhorias, correções ou sugestões!
