# Как быстро создать и сминтить NFT: стартовая версия за 10 минут

# Глава 1. Что такое NFT и смарт-контракты

В этом руководстве мы пройдём полный замкнутый цикл: напишем с нуля смарт-контракт NFT, развернём его в тестовой сети Ethereum, сминтим собственный NFT и посмотрим на него в OpenSea. Весь процесс использует браузерные инструменты без настройки локального окружения и может быть завершён за 10 минут.

Для этого руководства вам как минимум потребуется:

- Браузер Chrome (с установленным расширением-кошельком MetaMask)
- Аккаунт кошелька MetaMask
- Небольшое количество тестового ETH сети Sepolia (можно получить бесплатно, показано ниже)

> **Ноль затрат, ноль настройки**: весь процесс использует браузерные инструменты (Remix IDE), установка Node.js / Hardhat не нужна; код использует официальные безопасные шаблоны OpenZeppelin; после минтинга вы сможете посмотреть свой NFT в тестовой сети OpenSea.

## 1.1 Что такое NFT?

NFT (Non-Fungible Token, невзаимозаменяемый токен) — это тип цифрового актива в блокчейне. В отличие от взаимозаменяемых токенов, таких как Bitcoin или Ether, каждый NFT уникален, как нет в мире двух абсолютно одинаковых картин.

NFT можно понимать как **«сертификат коллекции в цифровом мире»**. Он может представлять:

* право собственности на цифровое произведение искусства
* билет на мероприятие
* игровой предмет
* учебный сертификат
* и даже твит

Основная ценность NFT в том, что **они используют технологию блокчейна, чтобы доказать, что «этот цифровой предмет принадлежит вам», и это доказательство является публичным, прозрачным и защищённым от подделки.**

<!-- ![placeholder: A concept diagram of NFTs: a digital artwork on the left, ownership record on blockchain on the right, connected by arrows](../../../../ru-ru/stage-3/cross-platform/nft-minting/images/image1.png) -->

## 1.2 Что такое смарт-контракт?

Смарт-контракт — это фрагмент кода, который работает в блокчейне. Вы можете думать о нём как об **«автоматически исполняемом контракте»**. После развёртывания в блокчейне он автоматически работает согласно логике кода, и никто не может его подделать.

NFT создаются и управляются через смарт-контракты. Когда вы «минтите» NFT, вы на самом деле вызываете функцию в смарт-контракте, чтобы записать в блокчейн: «NFT #0 принадлежит адресу вашего кошелька».

Мы будем использовать **Solidity** для написания контракта. Не волнуйтесь. С готовыми шаблонами от OpenZeppelin вам нужно написать менее 15 строк кода.

## 1.3 Какой NFT мы минтим?

Мы сминтим NFT **«Учебный сертификат Vibe Coder»**, чтобы подтвердить, что вы завершили это руководство и изучили основы разработки в блокчейне. Этот NFT будет:

* иметь уникальный token ID
* быть записанным в тестовой сети Ethereum Sepolia
* быть доступным для просмотра и отображения в тестовой сети OpenSea
* (опционально) включать ваше пользовательское изображение

Конечно, вы можете изменить его на любую тему, которая вам нравится: произведение искусства, сгенерированное AI, сувенирная карточка мероприятия, пиксельный аватар и многое другое. Содержимое NFT полностью на ваше усмотрение.

## 1.4 Зачем использовать тестовую сеть?

У Ethereum есть «основная сеть» (mainnet) и «тестовая сеть» (testnet):

| Сравнение | Основная сеть | Тестовая сеть (Sepolia) |
|------|----------------|------------------|
| Ценность ETH | Реальные деньги | Можно получить бесплатно, без реальной ценности |
| Стоимость развёртывания | Требует реальную плату за газ | Полностью бесплатно |
| Сценарий использования | Релиз в продакшен | Обучение, тестирование, разработка |
| Функциональные отличия | Нет | Те же, что и в основной сети |

Тестовая и основная сети функционально одинаковы. Единственное отличие в том, что тестовый ETH не имеет реальной ценности. Поэтому вы можете спокойно учиться и экспериментировать в тестовой сети, не беспокоясь о тратах.

## 1.5 План руководства

Мы пройдём весь путь в следующие шаги:

1. **Подготовка кошелька и тестового ETH** (2 минуты): установить MetaMask и получить бесплатный тестовый ETH
2. **Написание и развёртывание контракта** (4 минуты): написать контракт NFT в Remix IDE и развернуть в Sepolia
3. **Минтинг NFT и проверка результата** (4 минуты): вызвать контракт для минтинга NFT и проверить в OpenSea и Etherscan
4. **Продвинутое: добавление изображения к NFT** (опционально): сохранить изображение в IPFS, чтобы сделать NFT полным

# Глава 2. Подготовка кошелька и тестового ETH (2 минуты)

## 2.1 Установка кошелька MetaMask

MetaMask — самый популярный кошелёк Ethereum. Это браузерное расширение, которое позволяет взаимодействовать с блокчейн-приложениями.

1. Откройте Chrome и посетите [официальный сайт MetaMask](https://metamask.io/)
2. Нажмите **«Download»** и установите расширение для Chrome
3. После установки нажмите на иконку лисы MetaMask в правом верхнем углу
4. Выберите **«Create a new wallet»** и задайте пароль
5. **Важно**: храните свою фразу восстановления (12 слов) в безопасном месте. Потерять тестовый кошелёк не страшно, но хорошие привычки важны

<!-- ![placeholder: MetaMask installation and wallet creation flow screenshots: install extension -> create wallet -> set password -> backup recovery phrase](../../../../ru-ru/stage-3/cross-platform/nft-minting/images/image2.png) -->

## 2.2 Переключение на тестовую сеть Sepolia

MetaMask по умолчанию подключается к основной сети Ethereum. Нам нужно переключиться на тестовую сеть Sepolia:

1. Нажмите на выпадающий список сетей в верхней части MetaMask (по умолчанию: «Ethereum Mainnet»)
2. Нажмите **«Show test networks»**
3. Выберите **«Sepolia test network»**

Если вы не видите Sepolia, нажмите **«Add network»** и добавьте вручную:

| Параметр конфигурации | Значение |
|-------|-----|
| Имя сети | Sepolia test network |
| RPC URL | `https://rpc.sepolia.org` |
| Chain ID | 11155111 |
| Символ валюты | SepoliaETH |
| Обозреватель блоков | `https://sepolia.etherscan.io` |

<!-- ![placeholder: Screenshot of switching MetaMask to Sepolia testnet via network dropdown](../../../../ru-ru/stage-3/cross-platform/nft-minting/images/image3.png) -->

## 2.3 Получение бесплатного тестового ETH

Развёртывание контрактов и минтинг NFT требуют платы за газ. В тестовой сети газ оплачивается тестовым ETH, который бесплатен.

Посетите любой из кранов (faucet) ниже и введите адрес своего кошелька, чтобы получить бесплатный ETH сети Sepolia:

| Кран | URL | Сумма за получение | Требуется вход |
|--------|------|-----------|------------|
| QuickNode | `https://faucet.quicknode.com/ethereum/sepolia` | 0,1 ETH | Да |
| Alchemy | `https://www.alchemy.com/faucets/ethereum-sepolia` | 0,1 ETH | Да |
| Google Cloud | `https://cloud.google.com/application/web3/faucet/ethereum/sepolia` | 0,05 ETH | Да (аккаунт Google) |

> **Совет**: 0,1 тестового ETH достаточно для развёртывания контракта и минтинга десятков NFT. Если один кран не работает, попробуйте другой.

После успешного получения вернитесь в MetaMask, и ваш баланс должен измениться с 0 до 0,1 ETH (это может занять несколько секунд).

<!-- ![placeholder: Faucet website screenshot showing wallet address input and claiming test ETH](../../../../ru-ru/stage-3/cross-platform/nft-minting/images/image4.png) -->

# Глава 3. Написание и развёртывание смарт-контракта NFT (4 минуты)

## 3.1 Открытие Remix IDE

Remix — это официально рекомендуемая Ethereum онлайн-среда разработки смарт-контрактов. Она работает полностью в браузере и не требует установки.

Откройте: **https://remix.ethereum.org/**

Вы увидите интерфейс, похожий на VS Code: проводник файлов слева, редактор кода посередине и панель компиляции/развёртывания справа.

<!-- ![placeholder: Remix IDE home screenshot showing file explorer, code editor, and right-side panel](../../../../ru-ru/stage-3/cross-platform/nft-minting/images/image5.png) -->

## 3.2 Создание файла контракта

1. В проводнике файлов слева нажмите на папку **«contracts»**
2. Нажмите кнопку **«+»** сверху, чтобы создать новый файл
3. Назовите его **`MySimpleNFT.sol`**
4. Вставьте код ниже:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

// Import OpenZeppelin official secure ERC721 template
import "@openzeppelin/contracts/token/ERC721/ERC721.sol";

// Simplest NFT contract: name, symbol, mint function only
contract MySimpleNFT is ERC721 {
    uint256 private _tokenId;

    // Initialize collection name and symbol
    constructor() ERC721("VibeCoder", "VIBE") {}

    // Mint NFT: call once to mint one token to caller
    function mint() public {
        _safeMint(msg.sender, _tokenId);
        _tokenId++;
    }
}
```

**Разбор кода (менее 15 строк, и каждая строка понятна):**

| Код | Значение |
|------|------|
| `pragma solidity ^0.8.20` | Указать версию компилятора Solidity |
| `import "@openzeppelin/..."` | Импортировать стандартную реализацию ERC721 от OpenZeppelin (прошедший аудит безопасности шаблон) |
| `contract MySimpleNFT is ERC721` | Создать контракт, наследующий стандарт ERC721 |
| `ERC721("VibeCoder", "VIBE")` | Задать имя коллекции «VibeCoder» и символ «VIBE» |
| `_safeMint(msg.sender, _tokenId)` | Сминтить новый NFT для вызывающего |
| `_tokenId++` | Увеличить token ID после каждого минтинга |

> **Что такое ERC721?** Это стандарт NFT в Ethereum, определяющий базовые возможности NFT (передача, запрос владельца и т. д.). OpenZeppelin предоставляет прошедшую аудит безопасности реализацию, поэтому мы можем наследовать её напрямую, а не строить с нуля.

<!-- ![placeholder: Screenshot of contract code pasted in Remix IDE](../../../../ru-ru/stage-3/cross-platform/nft-minting/images/image6.png) -->

## 3.3 Компиляция контракта

1. Нажмите **«Solidity Compiler»** в левой панели (иконка молотка)
2. Выберите версию компилятора **0.8.20** (или выше в 0.8.x)
3. Нажмите **«Compile MySimpleNFT.sol»**
4. Зелёная галочка ✅ означает успешную компиляцию

> Если возникла ошибка, проверьте, совпадает ли версия Solidity и правильный ли путь импорта OpenZeppelin. Remix автоматически загружает зависимости OpenZeppelin из npm.

<!-- ![placeholder: Remix compile success screenshot with green check and selected compiler version](../../../../ru-ru/stage-3/cross-platform/nft-minting/images/image7.png) -->

## 3.4 Развёртывание контракта в тестовой сети Sepolia

1. Нажмите **«Deploy & Run Transactions»** в левой панели (иконка Ethereum)
2. Установите **Environment** в **«Injected Provider - MetaMask»**
   - Это автоматически подключит ваш кошелёк MetaMask
   - MetaMask покажет запрос на подключение, нажмите **«Connect»**
3. Убедитесь, что сеть — **Sepolia (11155111)**
4. Выберите **MySimpleNFT** в выпадающем списке Contract
5. Нажмите **«Deploy»**
6. MetaMask покажет подтверждение транзакции, нажмите **«Confirm»** (газ очень низкий; тестовая сеть бесплатна)

Через несколько секунд, когда развёртывание завершится успешно, раздел **«Deployed Contracts»** ниже покажет адрес вашего контракта. **Скопируйте и сохраните этот адрес**; он понадобится вам позже.

<!-- ![placeholder: Remix deployment screenshot showing environment selection, MetaMask confirmation, Deploy button, and deployed contract address](../../../../ru-ru/stage-3/cross-platform/nft-minting/images/image8.png) -->

# Глава 4. Минтинг NFT и проверка результата (4 минуты)

## 4.1 Минтинг вашего первого NFT

После успешного развёртывания в разделе **«Deployed Contracts»** в Remix вы увидите панель взаимодействия с контрактом.

1. Разверните панель контракта и найдите кнопку **«mint»** (оранжевую)
2. Нажмите **«mint»** напрямую (входные параметры не требуются)
3. MetaMask покажет подтверждение транзакции, нажмите **«Confirm»**
4. Подождите несколько секунд до завершения

Поздравляем! Вы только что сминтили NFT #0, и теперь он принадлежит адресу вашего кошелька.

Вы можете продолжать нажимать «mint», чтобы создать больше. Token ID автоматически увеличиваются каждый раз (#1, #2, #3...).

<!-- ![placeholder: Screenshot of clicking mint in Remix and confirming transaction in MetaMask](../../../../ru-ru/stage-3/cross-platform/nft-minting/images/image9.png) -->

## 4.2 Проверка результата минтинга

**Способ 1: проверка в Remix**

В панели контракта найдите **«balanceOf»** (синяя кнопка), введите адрес вашего кошелька и вызовите её. Если она возвращает `1` (или количество, которое вы сминтили), минтинг прошёл успешно.

Вы также можете вызвать **«ownerOf»**, ввести `0` (token ID), и она вернёт адрес вашего кошелька, доказывая, что NFT #0 принадлежит вам.

**Способ 2: проверка в Etherscan (рекомендуется)**

1. Откройте [Sepolia Etherscan](https://sepolia.etherscan.io/)
2. Вставьте **адрес вашего контракта** в поиск
3. Вы увидите страницу с деталями контракта со всеми записями транзакций
4. Нажмите **«Token Tracker»**, чтобы посмотреть все NFT, сминченные вашим контрактом

В Etherscan у каждой транзакции минтинга есть полные записи: кто сминтил, когда сминтил и token ID. В этом и есть прелесть блокчейна, который является «публичным, прозрачным и защищённым от подделки».

<!-- ![placeholder: Screenshot of viewing contract and NFT mint records on Sepolia Etherscan, including transaction list and Token Tracker](../../../../ru-ru/stage-3/cross-platform/nft-minting/images/image10.png) -->

# Глава 5. Продвинутое — добавление изображения к NFT (опционально)

NFT, сминченные до сих пор, имеют только ID, без изображения или описания. Чтобы сделать NFT полными, нам нужен **IPFS (InterPlanetary File System)** для хранения изображений и метаданных.

## 5.1 Что такое IPFS?

IPFS — это децентрализованная сеть хранения файлов. В отличие от обычного облачного хранилища, файлы в IPFS не зависят от одного сервера, а распределены по узлам по всему миру. Это означает:

* файлы не теряются, если один сервер выходит из строя
* содержимое файлов однозначно идентифицируется хешами и не может быть подделано
* это идеально подходит для хранения изображений и метаданных NFT

## 5.2 Загрузка изображения в Pinata

[Pinata](https://pinata.cloud/) — самый популярный сервис хранения IPFS. Бесплатный тариф предоставляет 1 ГБ хранилища, чего нам достаточно.

1. Посетите https://pinata.cloud/ и зарегистрируйте бесплатный аккаунт
2. После входа нажмите **«Upload»** -> **«File»**
3. Выберите изображение, которое хотите использовать в качестве произведения для NFT (изображение, сгенерированное AI, подойдёт, как и любое другое)
4. После успешной загрузки скопируйте **CID** (строка вида `QmXyz...`)

URI вашего изображения: `ipfs://yourCID`

<!-- ![placeholder: Screenshot of image upload in Pinata, including upload button and resulting CID](../../../../ru-ru/stage-3/cross-platform/nft-minting/images/image11.png) -->

## 5.3 Создание JSON метаданных

Метаданные NFT — это JSON-файл, описывающий имя, описание и URI изображения NFT. Создайте `metadata.json`:

```json
{
  "name": "Vibe Coder Certificate #0",
  "description": "This NFT certifies that the holder has completed the NFT minting tutorial and entered the world of Web3.",
  "image": "ipfs://your-image-cid",
  "attributes": [
    { "trait_type": "Course", "value": "Easy Vibe" },
    { "trait_type": "Skill", "value": "Smart Contract" },
    { "trait_type": "Level", "value": "Beginner" }
  ]
}
```

Загрузите `metadata.json` в Pinata тоже и получите CID метаданных.

## 5.4 Обновление контракта для поддержки изображений

Чтобы включить изображения в NFT, нам нужно немного обновить контракт, добавив `tokenURI`. Вернитесь в Remix и создайте новый файл `MyNFTWithImage.sol`:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";

contract MyNFTWithImage is ERC721, ERC721URIStorage {
    uint256 private _tokenId;

    constructor() ERC721("VibeCoder", "VIBE") {}

    // Pass metadata URI when minting
    function mint(string memory uri) public {
        _safeMint(msg.sender, _tokenId);
        _setTokenURI(_tokenId, uri);
        _tokenId++;
    }

    // Overrides required by Solidity
    function tokenURI(uint256 tokenId)
        public view override(ERC721, ERC721URIStorage)
        returns (string memory)
    {
        return super.tokenURI(tokenId);
    }

    function supportsInterface(bytes4 interfaceId)
        public view override(ERC721, ERC721URIStorage)
        returns (bool)
    {
        return super.supportsInterface(interfaceId);
    }
}
```

После развёртывания вызовите `mint` и передайте URI ваших метаданных (например, `ipfs://QmAbc.../metadata.json`). Тогда сминченный вами NFT будет включать изображение и описание.

<!-- ![placeholder: Screenshot of NFT details with image shown on Etherscan](../../../../ru-ru/stage-3/cross-platform/nft-minting/images/image12.png) -->

# Глава 6. Заключение

Поздравляем! Вы завершили полный цикл разработки NFT с нуля. Давайте вспомним:

1. Разобрались в основных концепциях NFT и смарт-контрактов
2. Установили MetaMask и переключились на тестовую сеть Sepolia
3. Написали смарт-контракт NFT менее чем в 15 строк в Remix IDE
4. Развернули контракт в тестовой сети Ethereum
5. Сминтили собственный NFT и проверили его в Etherscan
6. (Опционально) Научились добавлять изображение и метаданные с помощью IPFS

Весь процесс не потребовал установки локального окружения, не стоил денег и был полностью выполнен в браузере. В этом и есть привлекательность разработки в блокчейне: порог входа гораздо ниже, чем большинство людей ожидает.

**Продвинутые направления:**

* **Использование Hardhat / Foundry для локальной разработки**: когда логика контракта становится сложной, Remix уже недостаточно. Hardhat и Foundry — это профессиональные локальные фреймворки с автоматизированным тестированием, развёртыванием на основе скриптов, оптимизацией газа и многим другим
* **Добавление вайтлиста и лимитов минтинга**: контролируйте, кто может минтить, максимальное число минтов на кошелёк, цену минтинга и похожие правила
* **Создание фронтенда для минтинга**: используйте React + ethers.js / viem для создания отполированной страницы минтинга для веб-минтинга в один клик
* **Изучение мультиэкземплярных NFT ERC1155**: ERC1155 позволяет иметь несколько копий под одним token ID, что полезно для игровых предметов и билетов
* **Развёртывание в основной сети**: когда будете готовы, разверните в основной сети Ethereum (или в L2-сетях вроде Polygon или Base с более низкой платой за газ)

***Ваш первый NFT уже в блокчейне. Дверь в мир блокчейна теперь открыта.***

# Источники

* [Документация OpenZeppelin ERC721](https://docs.openzeppelin.com/contracts/5.x/erc721)
* [Официальная документация Remix IDE](https://remix-ide.readthedocs.io/)
* [Официальная документация MetaMask](https://docs.metamask.io/)
* [Официальная документация Solidity](https://docs.soliditylang.org/)
* [Sepolia Etherscan](https://sepolia.etherscan.io/)
* [Сервис хранения IPFS Pinata](https://pinata.cloud/)
* [Спецификация стандарта ERC721 (EIP-721)](https://eips.ethereum.org/EIPS/eip-721)
