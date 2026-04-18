<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hircle · Регистрация</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            background: #f5f5f7;
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', Helvetica, Arial, sans-serif;
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 20px;
        }

        .card {
            background: #ffffff;
            width: 100%;
            max-width: 440px;
            padding: 36px 32px 42px 32px;
            border-radius: 28px;
            box-shadow: 0 2px 8px rgba(0, 0, 0, 0.02), 0 4px 16px rgba(0, 0, 0, 0.02);
        }

        h1 {
            font-size: 28px;
            font-weight: 500;
            letter-spacing: -0.3px;
            color: #1d1d1f;
            margin-bottom: 6px;
        }

        .sub {
            font-size: 15px;
            color: #86868b;
            margin-bottom: 32px;
            font-weight: 400;
        }

        .input-group {
            margin-bottom: 24px;
        }

        label {
            display: block;
            font-size: 13px;
            font-weight: 500;
            color: #1d1d1f;
            margin-bottom: 8px;
            letter-spacing: -0.2px;
        }

        input {
            width: 100%;
            padding: 14px 0 14px 0;
            font-size: 16px;
            border: none;
            border-bottom: 1px solid #d2d2d6;
            background: transparent;
            outline: none;
            transition: border 0.2s;
            font-weight: 400;
            color: #1d1d1f;
        }

        input:focus {
            border-bottom-color: #0066cc;
        }

        .register-btn {
            width: 100%;
            background: #1d1d1f;
            color: white;
            border: none;
            border-radius: 44px;
            padding: 14px 20px;
            font-size: 17px;
            font-weight: 500;
            margin-top: 32px;
            cursor: pointer;
            transition: background 0.2s;
        }

        .register-btn:hover {
            background: #3a3a3c;
        }

        .message {
            margin-top: 24px;
            padding: 12px 0 0 0;
            font-size: 13px;
            text-align: center;
            display: none;
        }

        .message.success {
            color: #1e7b4b;
            display: block;
        }

        .message.error {
            color: #e5484d;
            display: block;
        }

        hr {
            margin: 32px 0 0 0;
            border: none;
            border-top: 0.5px solid #e9e9ef;
        }

        .footer-note {
            margin-top: 20px;
            text-align: center;
            font-size: 12px;
            color: #8e8e93;
        }
    </style>
</head>
<body>
<div class="card">
    <h1>Hircle</h1>
    <div class="sub">Регистрация</div>

    <div class="input-group">
        <label>Имя</label>
        <input type="text" id="fullName" autocomplete="name" placeholder="">
    </div>

    <div class="input-group">
        <label>Email</label>
        <input type="email" id="email" autocomplete="email" placeholder="">
    </div>

    <div class="input-group">
        <label>Телефон</label>
        <input type="tel" id="phone" autocomplete="tel" placeholder="">
    </div>

    <button id="registerBtn" class="register-btn">Зарегистрироваться</button>

    <div id="message" class="message"></div>
    <hr />
    <div class="footer-note">Hircle service</div>
</div>

<script>
    const fullNameInput = document.getElementById('fullName');
    const emailInput = document.getElementById('email');
    const phoneInput = document.getElementById('phone');
    const registerBtn = document.getElementById('registerBtn');
    const messageDiv = document.getElementById('message');

    function showMessage(text, type) {
        messageDiv.textContent = text;
        messageDiv.className = `message ${type}`;
        setTimeout(() => {
            messageDiv.style.display = 'none';
            messageDiv.className = 'message';
        }, 3000);
    }

    function registerAndRedirect() {
        const name = fullNameInput.value.trim();
        const email = emailInput.value.trim();
        const phone = phoneInput.value.trim();

        if (!name) {
            showMessage('Введите имя', 'error');
            return;
        }
        if (!email) {
            showMessage('Введите email', 'error');
            return;
        }
        const emailPattern = /^[^\s@]+@([^\s@]+\.)+[^\s@]+$/;
        if (!emailPattern.test(email)) {
            showMessage('Укажите корректный email', 'error');
            return;
        }
        if (!phone) {
            showMessage('Введите номер телефона', 'error');
            return;
        }

        const phoneClean = phone.replace(/\s/g, '');
        if (phoneClean.length < 9) {
            showMessage('Некорректный номер телефона', 'error');
            return;
        }

        // сохраняем пользователя в localStorage
        let users = [];
        const stored = localStorage.getItem('hircle_users');
        if (stored) {
            try {
                users = JSON.parse(stored);
            } catch(e) { users = []; }
        }

        const duplicate = users.find(u => u.email === email || u.phone === phone);
        if (duplicate) {
            showMessage('Этот email или телефон уже зарегистрированы', 'error');
            return;
        }

        const generateId = () => {
            const chars = 'ABCDEFGHJKLMNPQRSTUVWXYZ0123456789';
            let id = '';
            for (let i = 0; i < 9; i++) {
                id += chars.charAt(Math.floor(Math.random() * chars.length));
            }
            return id;
        };
        const userId = generateId();

        const newUser = {
            id: userId,
            name: name,
            email: email,
            phone: phone,
            registeredAt: new Date().toISOString()
        };

        users.push(newUser);
        localStorage.setItem('hircle_users', JSON.stringify(users));
        localStorage.setItem('hircle_current_user', JSON.stringify(newUser));

        showMessage(`Добро пожаловать, ${name}!`, 'success');

        setTimeout(() => {
            window.location.href = 'https://warden200.github.io/Hircle.register.org/';
        }, 1000);
    }

    registerBtn.addEventListener('click', registerAndRedirect);

    const fields = [fullNameInput, emailInput, phoneInput];
    fields.forEach(field => {
        field.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') {
                e.preventDefault();
                registerAndRedirect();
            }
        });
    });
</script>
</body>
</html>
