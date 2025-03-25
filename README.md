# Fortuna-dimensional-
<!DOCTYPE html>
<html lang="es">
<head>

    <meta charset="UTF-8">

    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title>Fortuna Dimensional</title>

    <script src="https://cdn.jsdelivr.net/npm/phaser@3.55.2/dist/phaser.min.js"></script>

    <style> 
    body {
        margin: 0;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        background: black;
    }
    </style>

</head>

<body>

<script>
const config = {
    type: Phaser.AUTO,
    width: 800,
    height: 600,
    scene: {
        preload: preload,
        create: create,
        update: update
    }
};

const symbols = ['diamante', 'cereza', 'bar', 'siete', 'joker'];

const dimensions = [
    { color: "#222222", name: "Tierra" },
    { color: "#FF4500", name: "Fuego" },
    { color: "#00BFFF", name: "Hielo" }
];

let game = new Phaser.Game(config);
let reels = [];
let isSpinning = false;
let dimension = 0;
let spinSound, winSound;
let winText;

function preload() {
    this.load.audio('spin', 'https://assets.codepen.io/605876/spin.mp3');
    this.load.audio('win', 'https://assets.codepen.io/605876/win.mp3');

    for (let sym of symbols) {
        this.load.image(sym, `https://assets.codepen.io/605876/${sym}.png`);
    }
}

function create() {
    let bg = this.add.rectangle(400, 300, 800, 600, Phaser.Display.Color.HexStringToColor(dimensions[dimension].color).color);

    spinSound = this.sound.add('spin');
    winSound = this.sound.add('win');

    for (let i = 0; i < 3; i++) {
        let symbol = this.add.image(200 + i * 200, 300, Phaser.Math.RND.pick(symbols)).setScale(0.5);
        reels.push(symbol);
    }

    let spinButton = this.add.text(350, 500, 'GIRAR', { fontSize: '32px', fill: '#FFF' })
        .setInteractive()
        .on('pointerdown', () => spinReels());

    let dimensionButton = this.add.text(280, 550, 'CAMBIAR DIMENSIÓN', { fontSize: '24px', fill: '#FFF' })
        .setInteractive()
        .on('pointerdown', () => changeDimension(bg));

    // Texto de victoria oculto inicialmente
    winText = this.add.text(300, 100, '', { fontSize: '32px', fill: '#FFD700' }).setVisible(false);
}

function spinReels() {
    if (isSpinning) return;
    
    isSpinning = true;
    winText.setVisible(false);
    spinSound.play();

    reels.forEach((reel, index) => {
        setTimeout(() => {
            let newSymbol = Phaser.Math.RND.pick(symbols);
            reel.setTexture(newSymbol);

            if (index === 2) {
                setTimeout(() => {
                    isSpinning = false;
                    checkWin();
                }, 500);
            }
        }, index * 500);
    });
}

function checkWin() {
    if (reels[0].texture.key === reels[1].texture.key && reels[1].texture.key === reels[2].texture.key) {
        winSound.play();
        winText.setText('¡Ganaste con ' + reels[0].texture.key + '!').setVisible(true);
    }
}

function changeDimension(bg) {
    dimension = (dimension + 1) % dimensions.length;
    bg.setFillStyle(Phaser.Display.Color.HexStringToColor(dimensions[dimension].color).color);
}

</script>

</body>

</html>
