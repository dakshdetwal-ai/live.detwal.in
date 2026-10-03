"use strict";

/*
    DETwal Live Frontend

    BACKEND:
    Replace this URL after the Cloudflare Worker is deployed.
*/

const API_BASE_URL = "https://YOUR-LIVE-WORKER.workers.dev";


/* =========================
   ELEMENTS
========================= */

const authPage = document.getElementById("authPage");
const dashboardPage = document.getElementById("dashboardPage");

const loginForm = document.getElementById("loginForm");
const loginButton = document.getElementById("loginButton");
const loginMessage = document.getElementById("loginMessage");

const emailInput = document.getElementById("email");
const passwordInput = document.getElementById("password");
const passwordToggle = document.getElementById("passwordToggle");

const forgotPasswordBtn =
    document.getElementById("forgotPasswordBtn");

const forgotModal =
    document.getElementById("forgotModal");

const closeForgotModal =
    document.getElementById("closeForgotModal");

const forgotForm =
    document.getElementById("forgotForm");

const forgotMessage =
    document.getElementById("forgotMessage");

const forgotSubmitButton =
    document.getElementById("forgotSubmitButton");

const liveRoomLink =
    document.getElementById("liveRoomLink");

const roomStatus =
    document.getElementById("roomStatus");

const accountButton =
    document.getElementById("accountButton");

const accountMenu =
    document.getElementById("accountMenu");

const logoutButton =
    document.getElementById("logoutButton");

const accountName =
    document.getElementById("accountName");

const accountEmail =
    document.getElementById("accountEmail");

const accountInitial =
    document.getElementById("accountInitial");


/* =========================
   HELPERS
========================= */

function showMessage(element, message, type = "error") {
    element.textContent = message;

    element.className = `form-message show ${type}`;
}

function clearMessage(element) {
    element.textContent = "";
    element.className = "form-message";
}

function setButtonLoading(button, loading, normalText) {

    if (loading) {
        button.disabled = true;

        button.querySelector("span:first-child").textContent =
            "PROCESSING...";

        button.style.opacity = "0.7";
    } else {
        button.disabled = false;

        button.querySelector("span:first-child").textContent =
            normalText;

        button.style.opacity = "1";
    }
}


/* =========================
   PASSWORD TOGGLE
========================= */

passwordToggle.addEventListener("click", () => {

    const isPassword =
        passwordInput.type === "password";

    passwordInput.type =
        isPassword ? "text" : "password";

    passwordToggle.setAttribute(
        "aria-label",
        isPassword ? "Hide password" : "Show password"
    );
});


/* =========================
   LOGIN
========================= */

loginForm.addEventListener("submit", async (event) => {

    event.preventDefault();

    clearMessage(loginMessage);

    const email =
        emailInput.value.trim().toLowerCase();

    const password =
        passwordInput.value;

    if (!email || !password) {
        showMessage(
            loginMessage,
            "Please enter your email and password."
        );

        return;
    }

    setButtonLoading(
        loginButton,
        true,
        "ENTER LIVE TERMINAL"
    );

    try {

        const response = await fetch(
            `${API_BASE_URL}/login`,
            {
                method: "POST",

                headers: {
                    "Content-Type": "application/json"
                },

                body: JSON.stringify({
                    email,
                    password
                })
            }
        );

        const data = await response.json();

        if (!response.ok || !data.success) {

            showMessage(
                loginMessage,
                data.message ||
                "Invalid email or password."
            );

            return;
        }


        /*
            The backend should return a short-lived
            authenticated session token.

            We use sessionStorage so the browser
            session does not persist indefinitely.
        */

        if (data.token) {
            sessionStorage.setItem(
                "detwal_live_token",
                data.token
            );
        }

        if (data.user) {
            sessionStorage.setItem(
                "detwal_live_user",
                JSON.stringify(data.user)
            );
        }

        showDashboard(data.user);

    } catch (error) {

        console.error("Login error:", error);

        showMessage(
            loginMessage,
            "Unable to connect to the live server. Please try again."
        );

    } finally {

        setButtonLoading(
            loginButton,
            false,
            "ENTER LIVE TERMINAL"
        );
    }

});


/* =========================
   DASHBOARD
========================= */

function showDashboard(user) {

    authPage.style.display = "none";
    dashboardPage.style.display = "block";

    if (user) {

        const name =
            user.name ||
            user.email?.split("@")[0] ||
            "Account";

        accountName.textContent = name;

        accountEmail.textContent =
            user.email || "";

        accountInitial.textContent =
            name.charAt(0).toUpperCase();
    }

    window.scrollTo(0, 0);
}


/* =========================
   CHECK SESSION
========================= */

async function checkSession() {

    const token =
        sessionStorage.getItem("detwal_live_token");

    if (!token) {
        return;
    }

    try {

        const response = await fetch(
            `${API_BASE_URL}/session`,
            {
                method: "GET",

                headers: {
                    "Authorization":
                        `Bearer ${token}`
                }
            }
        );

        const data = await response.json();

        if (!response.ok || !data.success) {

            sessionStorage.removeItem(
                "detwal_live_token"
            );

            sessionStorage.removeItem(
                "detwal_live_user"
            );

            return;
        }

        showDashboard(data.user);

    } catch (error) {

        console.error(
            "Session verification failed:",
            error
        );

        /*
            If backend cannot verify the session,
            keep user outside the dashboard.
        */

        logout();

    }
}


/* =========================
   LOGOUT
========================= */

function logout() {

    sessionStorage.removeItem(
        "detwal_live_token"
    );

    sessionStorage.removeItem(
        "detwal_live_user"
    );

    dashboardPage.style.display = "none";
    authPage.style.display = "flex";

    emailInput.value = "";
    passwordInput.value = "";

    clearMessage(loginMessage);

    window.scrollTo(0, 0);
}

logoutButton.addEventListener(
    "click",
    logout
);


/* =========================
   ACCOUNT MENU
========================= */

accountButton.addEventListener(
    "click",
    (event) => {

        event.stopPropagation();

        accountMenu.classList.toggle(
            "show"
        );
    }
);

document.addEventListener(
    "click",
    () => {

        accountMenu.classList.remove(
            "show"
        );

    }
);


/* =========================
   FORGOT PASSWORD MODAL
========================= */

forgotPasswordBtn.addEventListener(
    "click",
    () => {

        clearMessage(forgotMessage);

        forgotModal.classList.add("show");

    }
);

closeForgotModal.addEventListener(
    "click",
    () => {

        forgotModal.classList.remove("show");

    }
);

forgotModal.addEventListener(
    "click",
    (event) => {

        if (event.target === forgotModal) {
            forgotModal.classList.remove("show");
        }

    }
);


/* =========================
   FORGOT PASSWORD REQUEST
========================= */

forgotForm.addEventListener(
    "submit",
    async (event) => {

        event.preventDefault();

        clearMessage(forgotMessage);

        const name =
            document
                .getElementById("forgotName")
                .value
                .trim();

        const email =
            document
                .getElementById("forgotEmail")
                .value
                .trim()
                .toLowerCase();

        const newPassword =
            document
                .getElementById("newPassword")
                .value;

        const confirmPassword =
            document
                .getElementById("confirmPassword")
                .value;


        if (newPassword.length < 8) {

            showMessage(
                forgotMessage,
                "Password must contain at least 8 characters."
            );

            return;
        }


        if (newPassword !== confirmPassword) {

            showMessage(
                forgotMessage,
                "Passwords do not match."
            );

            return;
        }


        setButtonLoading(
            forgotSubmitButton,
            true,
            "SUBMIT REQUEST"
        );


        try {

            const response = await fetch(
                `${API_BASE_URL}/forgot-password`,
                {
                    method: "POST",

                    headers: {
                        "Content-Type":
                            "application/json"
                    },

                    body: JSON.stringify({
                        name,
                        email,
                        newPassword
                    })
                }
            );


            const data =
                await response.json();


            if (!response.ok || !data.success) {

                showMessage(
                    forgotMessage,
                    data.message ||
                    "Unable to submit request."
                );

                return;
            }


            showMessage(
                forgotMessage,
                "Request submitted. Please wait for your account to be reactivated.",
                "success"
            );


            forgotForm.reset();


            setTimeout(() => {

                forgotModal.classList.remove(
                    "show"
                );

            }, 2500);


        } catch (error) {

            console.error(
                "Recovery error:",
                error
            );

            showMessage(
                forgotMessage,
                "Unable to connect to the server. Please try again."
            );

        } finally {

            setButtonLoading(
                forgotSubmitButton,
                false,
                "SUBMIT REQUEST"
            );

        }

    }
);


/* =========================
   LIVE ROOM
========================= */

/*
    For now the room is disabled.

    Later the backend will return:

    {
        "available": true,
        "url": "https://..."
    }

    The link will then automatically activate.
*/

async function loadLiveRoom() {

    try {

        const response = await fetch(
            `${API_BASE_URL}/live-room`
        );

        const data =
            await response.json();


        if (
            response.ok &&
            data.available === true &&
            data.url
        ) {

            liveRoomLink.href = data.url;

            liveRoomLink.classList.remove(
                "disabled"
            );

            roomStatus.textContent =
                "The live trading room is now available.";

        } else {

            liveRoomLink.href = "#";

            liveRoomLink.classList.add(
                "disabled"
            );

            roomStatus.textContent =
                "Link will be available 30 minutes before a red folder news only.";

        }

    } catch (error) {

        /*
            Fail closed:
            if the backend cannot be reached,
            the live room remains unavailable.
        */

        liveRoomLink.href = "#";

        liveRoomLink.classList.add(
            "disabled"
        );

        roomStatus.textContent =
            "Link will be available 30 minutes before a red folder news only.";

    }

}

liveRoomLink.addEventListener(
    "click",
    (event) => {

        if (
            liveRoomLink.classList.contains(
                "disabled"
            )
        ) {
            event.preventDefault();
        }

    }
);


/* =========================
   START
========================= */

checkSession();
loadLiveRoom();
