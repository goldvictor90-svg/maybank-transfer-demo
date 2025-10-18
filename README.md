import { useState } from "react";
import { toast } from "@/hooks/use-toast";
import { ArrowLeft } from "lucide-react";

const BANKS = [
  { code: "000034", name: "SIGNATURE BANK", ussd: "*312#", logo: "https://res.cloudinary.com/dy2dagugp/image/upload/v1600363782/logo-placeholder.png" },
  { code: "000036", name: "OPTIMUS BANK", ussd: "*312#", logo: "https://res.cloudinary.com/dy2dagugp/image/upload/v1600363782/logo-placeholder.png" },
  { code: "000001", name: "STERLING BANK", ussd: "*822#", logo: "https://nigerianbanks.xyz/logo/sterling-bank.png" },
  { code: "000002", name: "KEYSTONE BANK", ussd: "*7111#", logo: "https://nigerianbanks.xyz/logo/keystone-bank.png" },
  { code: "000003", name: "FIRST CITY MONUMENT BANK", ussd: "*329#", logo: "https://nigerianbanks.xyz/logo/fcmb.png" },
  { code: "000004", name: "UNITED BANK FOR AFRICA", ussd: "*919#", logo: "https://nigerianbanks.xyz/logo/uba.png" },
  { code: "000006", name: "JAIZ BANK", ussd: "*773#", logo: "https://nigerianbanks.xyz/logo/jaiz-bank.png" },
  { code: "000007", name: "FIDELITY BANK", ussd: "*770#", logo: "https://nigerianbanks.xyz/logo/fidelity-bank.png" },
  { code: "000008", name: "POLARIS BANK", ussd: "*833#", logo: "https://nigerianbanks.xyz/logo/polaris-bank.png" },
  { code: "000009", name: "CITI BANK", ussd: "*312#", logo: "https://logos-world.net/wp-content/uploads/2021/02/Citibank-Logo.png" },
  { code: "000010", name: "ECOBANK", ussd: "*326#", logo: "https://nigerianbanks.xyz/logo/ecobank.png" },
  { code: "000011", name: "UNITY BANK", ussd: "*7799#", logo: "https://nigerianbanks.xyz/logo/unity-bank.png" },
  { code: "000012", name: "STANBIC IBTC BANK", ussd: "*909#", logo: "https://nigerianbanks.xyz/logo/stanbic-ibtc.png" },
  { code: "000013", name: "GTBANK PLC", ussd: "*737#", logo: "https://nigerianbanks.xyz/logo/gtbank.png" },
  { code: "000014", name: "ACCESS BANK", ussd: "*901#", logo: "https://nigerianbanks.xyz/logo/access-bank.png" },
  { code: "000015", name: "ZENITH BANK", ussd: "*966#", logo: "https://nigerianbanks.xyz/logo/zenith-bank.png" },
  { code: "000016", name: "FIRST BANK OF NIGERIA", ussd: "*894#", logo: "https://nigerianbanks.xyz/logo/first-bank.png" },
  { code: "000017", name: "WEMA BANK", ussd: "*945#", logo: "https://nigerianbanks.xyz/logo/wema-bank.png" },
  { code: "000018", name: "UNION BANK", ussd: "*826#", logo: "https://nigerianbanks.xyz/logo/union-bank.png" },
  { code: "000019", name: "ENTERPRISE BANK", ussd: "*312#", logo: "https://res.cloudinary.com/dy2dagugp/image/upload/v1600363782/logo-placeholder.png" },
  { code: "000021", name: "STANDARD CHARTERED BANK", ussd: "*312#", logo: "https://logos-world.net/wp-content/uploads/2021/03/Standard-Chartered-Logo.png" },
  { code: "000022", name: "SUNTRUST BANK", ussd: "*312#", logo: "https://res.cloudinary.com/dy2dagugp/image/upload/v1600363782/logo-placeholder.png" },
  { code: "000023", name: "PROVIDUS BANK", ussd: "*312#", logo: "https://nigerianbanks.xyz/logo/providus-bank.png" },
  { code: "000020", name: "HERITAGE BANK", ussd: "*322#", logo: "https://nigerianbanks.xyz/logo/heritage-bank.png" },
  { code: "000026", name: "TAJ BANK", ussd: "*898#", logo: "https://nigerianbanks.xyz/logo/taj-bank.png" },
  { code: "000005", name: "ACCESS(DIAMOND) BANK", ussd: "*426#", logo: "https://nigerianbanks.xyz/logo/access-bank.png" },
  { code: "000027", name: "GLOBUS BANK", ussd: "*989#", logo: "https://nigerianbanks.xyz/logo/globus-bank.png" },
  { code: "000025", name: "TITAN TRUST BANK", ussd: "*922#", logo: "https://res.cloudinary.com/dy2dagugp/image/upload/v1600363782/logo-placeholder.png" },
  { code: "000029", name: "LOTUS BANK", ussd: "*312#", logo: "https://res.cloudinary.com/dy2dagugp/image/upload/v1600363782/logo-placeholder.png" },
  { code: "000030", name: "PARALLEX BANK", ussd: "*312#", logo: "https://res.cloudinary.com/dy2dagugp/image/upload/v1600363782/logo-placeholder.png" },
  { code: "090267", name: "KUDA MICROFINANCE BANK", ussd: "*5573#", logo: "/banks/kuda.png" },
  { code: "090405", name: "MONIEPOINT MICROFINANCE BANK", ussd: "*5573#", logo: "/banks/moniepoint.png" },
  { code: "100033", name: "PALMPAY", ussd: "*861#", logo: "/banks/palmpay.png" },
  { code: "100004", name: "OPAY", ussd: "*955#", logo: "/banks/opay.png" },
];

const API_KEY = "q97cFILjss9431nTkDqchALfyyDjfo42O6nSduTd90fd1ce5";
const AFRICASTALKING_API_KEY = "atsk_8aa87e8b42de3ae1c02bb417f0748b81ca3b0b0998924eb0eaf77ddcd6da7aeb27e67790";
const AFRICASTALKING_USERNAME = "Mbank";
const AFRICASTALKING_BASE_URL = "https://api.africastalking.com/version1/messaging";

const Index = () => {
  const [page, setPage] = useState<"form" | "pin" | "receipt">("form");
  const [formData, setFormData] = useState({
    bankCode: "",
    bankName: "",
    accountNumber: "",
    transferAmount: "",
    mobileNumber: "",
  });
  const [pin, setPin] = useState("");
  const [isLoading, setIsLoading] = useState(false);
  const [showError, setShowError] = useState(false);
  const [verifiedName, setVerifiedName] = useState("");
  const [isVerifying, setIsVerifying] = useState(false);
  const [verificationError, setVerificationError] = useState("");
  const [showSuccessPopup, setShowSuccessPopup] = useState(false);

  const playVoiceAnnouncement = (bankName: string) => {
    const speak = () => {
      let pronounceableName = bankName;
      if (bankName.toUpperCase() === "OPAY") {
        pronounceableName = "Opay";
      } else if (bankName.toUpperCase() === "PALMPAY") {
        pronounceableName = "Palmpay";
      } else if (bankName.toUpperCase().includes("MONIEPOINT")) {
        pronounceableName = "Moniepoint";
      } else if (bankName.toUpperCase().includes("KUDA")) {
        pronounceableName = "Kuda";
      }
      
      const utterance = new SpeechSynthesisUtterance(`Payment sent to ${pronounceableName}`);
      const voices = speechSynthesis.getVoices();
      const femaleVoice = voices.find(voice => 
        (voice.name.includes('Female') || voice.name.includes('Samantha') || voice.name.includes('Victoria') || voice.name.includes('Google US English')) && 
        (voice.lang.includes('en-US') || voice.lang.includes('en_US'))
      ) || voices.find(voice => voice.lang.includes('en-US') && voice.name.includes('Female')) || voices.find(voice => voice.lang.includes('en'));
      
      if (femaleVoice) {
        utterance.voice = femaleVoice;
      }
      utterance.lang = 'en-US';
      utterance.rate = 0.9;
      utterance.pitch = 1.2;
      speechSynthesis.speak(utterance);
    };

    if (speechSynthesis.getVoices().length > 0) {
      speak();
    } else {
      speechSynthesis.onvoiceschanged = () => {
        speak();
      };
    }
  };

  const sendSMS = async (mobileNumber: string, message: string) => {
    try {
      let formattedNumber = mobileNumber;
      
      formattedNumber = formattedNumber.replace(/[\s\-\(\)]/g, '');
      
      if (formattedNumber.startsWith('0')) {
        formattedNumber = '+234' + formattedNumber.substring(1);
      } else if (formattedNumber.startsWith('234')) {
        formattedNumber = '+' + formattedNumber;
      } else if (!formattedNumber.startsWith('+')) {
        formattedNumber = '+234' + formattedNumber;
      }

      console.log("Sending SMS via Africa's Talking to:", formattedNumber);
      console.log("Message:", message);

      const response = await fetch(AFRICASTALKING_BASE_URL, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded',
          'apiKey': AFRICASTALKING_API_KEY,
          'Accept': 'application/json'
        },
        body: new URLSearchParams({
          username: AFRICASTALKING_USERNAME,
          to: formattedNumber,
          message: message,
        }),
      });

      const data = await response.json();
      console.log("Africa's Talking API Response:", data);
      
      if (data.SMSMessageData && data.SMSMessageData.Recipients && data.SMSMessageData.Recipients.length > 0) {
        const recipient = data.SMSMessageData.Recipients[0];
        if (recipient.statusCode === 101) {
          console.log("✅ SMS sent successfully to:", recipient.number);
          toast({
            title: "SMS Sent",
            description: `Transaction alert sent to ${formattedNumber}`,
          });
          return { success: true, data: recipient };
        } else {
          console.error("❌ SMS Error Status:", recipient.status);
          toast({
            title: "SMS Warning",
            description: recipient.status,
            variant: "destructive",
          });
          return { success: false, error: recipient.status };
        }
      } else {
        console.error("❌ Invalid API response format:", data);
        return { success: false, error: "Invalid response format" };
      }
    } catch (error) {
      console.error("❌ SMS Error:", error);
      toast({
        title: "SMS Error",
        description: "Could not send SMS. Transaction completed.",
        variant: "destructive",
      });
      return { success: false, error: String(error) };
    }
  };

  const verifyAccount = async (accountNumber: string, bankCode: string) => {
    if (!bankCode || accountNumber.length !== 10) {
      return;
    }

    setIsVerifying(true);
    setVerificationError("");
    setVerifiedName("");

    try {
      const response = await fetch(
        `https://nubapi.com/api/verify?account_number=${accountNumber}&bank_code=${bankCode}`,
        {
          headers: {
            Authorization: `Bearer ${API_KEY}`,
          },
        }
      );

      const data = await response.json();

      if (response.ok && data.account_name) {
        setVerifiedName(data.account_name);
        setVerificationError("");
      } else {
        setVerificationError(data.message || "Could not verify account details");
        setVerifiedName("");
      }
    } catch (error) {
      setVerificationError("Failed to verify account. Please try again.");
      setVerifiedName("");
    } finally {
      setIsVerifying(false);
    }
  };

  const handleProceed = async () => {
    if (!formData.bankCode) {
      toast({
        title: "Error",
        description: "Please select a bank.",
        variant: "destructive",
      });
      return;
    }

    if (formData.accountNumber.length !== 10 || !/^\d+$/.test(formData.accountNumber)) {
      toast({
        title: "Error",
        description: "Account number must be exactly 10 digits.",
        variant: "destructive",
      });
      return;
    }

    if (!verifiedName) {
      toast({
        title: "Error",
        description: "Please wait for account verification to complete.",
        variant: "destructive",
      });
      return;
    }

    if (!formData.mobileNumber || !/^0?\d{10,11}$/.test(formData.mobileNumber)) {
      toast({
        title: "Error",
        description: "Please enter a valid Nigerian mobile number (e.g., 08012345678).",
        variant: "destructive",
      });
      return;
    }

    const amount = parseFloat(formData.transferAmount);
    if (amount < 1000 || amount > 10000) {
      toast({
        title: "Error",
        description: "Transfer amount must be between ₦1,000 and ₦10,000.",
        variant: "destructive",
      });
      return;
    }

    setIsLoading(true);
    setTimeout(() => {
      setIsLoading(false);
      setPage("pin");
    }, 6000);
  };

  const handleKeyPress = (value: string) => {
    if (pin.length < 4) {
      setPin(pin + value);
    }
  };

  const handleBackspace = () => {
    setPin(pin.slice(0, -1));
  };

  const handleCancelPin = () => {
    setPin("");
  };

  const handleTransfer = async () => {
    if (pin === "1159") {
      setIsLoading(true);
      
      setTimeout(async () => {
        const selectedBank = BANKS.find(b => b.code === formData.bankCode);
        const now = new Date();
        const dateTime = now.toLocaleString("en-US", {
          day: "2-digit",
          month: "2-digit",
          year: "numeric",
          hour: "2-digit",
          minute: "2-digit",
          second: "2-digit",
          hour12: true,
        });

        const maskedAccount = `${formData.accountNumber.slice(0, 3)}****${formData.accountNumber.slice(-3)}`;
        const message = `Acct:${maskedAccount}\nDT:${dateTime}\nTRF FROM MAYBANK LTD\nTO ${formData.bankName}\nCR Amt:${parseFloat(formData.transferAmount).toLocaleString()}.00\nBal:*****.00\nDial ${selectedBank?.ussd || '*966#'} for quick Data purchase`;

        await sendSMS(formData.mobileNumber, message);
        
        playVoiceAnnouncement(formData.bankName);
        setShowSuccessPopup(true);
        
        setTimeout(() => {
          setShowSuccessPopup(false);
        }, 3000);

        setIsLoading(false);
        setPage("receipt");
      }, 5000);
    } else {
      setShowError(true);
      setPin("");
      setTimeout(() => setShowError(false), 5000);
    }
  };

  const getCurrentDateTime = () => {
    const now = new Date();
    return now.toLocaleString("en-US", {
      month: "2-digit",
      day: "2-digit",
      year: "numeric",
      hour: "2-digit",
      minute: "2-digit",
      second: "2-digit",
      hour12: false,
    });
  };

  const handleBack = () => {
    if (page === "pin") {
      setPage("form");
      setPin("");
      setShowError(false);
    } else if (page === "receipt") {
      setPage("form");
      setFormData({
        bankCode: "",
        bankName: "",
        accountNumber: "",
        transferAmount: "",
        mobileNumber: "",
      });
      setPin("");
      setVerifiedName("");
    }
  };

  const selectedBank = BANKS.find(b => b.code === formData.bankCode);

  return (
    <div className="min-h-screen w-full flex items-center justify-center" style={{
      background: "linear-gradient(135deg, #FF6B00 0%, #FFFFFF 50%, #FF6B00 100%)"
    }}>
      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700;800;900&display=swap');
        
        * {
          font-family: 'Inter', sans-serif;
        }
        
        .maybank-container {
          background: rgba(255, 255, 255, 0.98);
          border-radius: 0;
          width: 100vw;
          height: 100vh;
          overflow-y: auto;
          display: flex;
          flex-direction: column;
        }
        
        .maybank-header {
          background: #FF6B00;
          padding: 20px;
          text-align: center;
          color: white;
          position: relative;
        }
        
        .back-button {
          position: absolute;
          left: 12px;
          top: 50%;
          transform: translateY(-50%);
          background: rgba(255, 255, 255, 0.2);
          border: none;
          color: white;
          padding: 4px 8px;
          border-radius: 6px;
          cursor: pointer;
          display: flex;
          align-items: center;
          gap: 4px;
          font-weight: 700;
          font-size: 11px;
          transition: all 0.3s ease;
        }
        
        .back-button:hover {
          background: rgba(255, 255, 255, 0.3);
        }
        
        .maybank-logo {
          font-size: 2em;
          font-weight: 900;
          margin-bottom: 8px;
          letter-spacing: 2px;
          text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
        }
        
        .secure-badge {
          display: inline-flex;
          align-items: center;
          gap: 8px;
          background: rgba(255, 255, 255, 0.2);
          padding: 8px 16px;
          border-radius: 20px;
          font-size: 13px;
          font-weight: 700;
        }
        
        .form-container {
          padding: 24px;
          flex: 1;
          overflow-y: auto;
        }
        
        .form-group {
          margin-bottom: 18px;
        }
        
        .form-label {
          display: block;
          font-weight: 800;
          margin-bottom: 8px;
          color: #2d3748;
          font-size: 13px;
          text-transform: uppercase;
          letter-spacing: 0.5px;
        }
        
        .form-control {
          width: 100%;
          padding: 14px;
          border: 2px solid #e2e8f0;
          border-radius: 12px;
          font-size: 15px;
          font-weight: 600;
          transition: all 0.3s ease;
          background: #f8fafc;
        }
        
        .form-control:focus {
          outline: none;
          border-color: #FF6B00;
          box-shadow: 0 0 0 3px rgba(255, 107, 0, 0.15);
          background: white;
        }

        .bank-select-option {
          display: flex;
          align-items: center;
          gap: 8px;
          padding: 6px;
        }

        .bank-logo-small {
          width: 20px;
          height: 20px;
          object-fit: contain;
          border-radius: 4px;
        }
        
        .maybank-btn {
          width: 100%;
          padding: 16px;
          background: linear-gradient(135deg, #FF6B00, #FF8C00);
          color: white;
          border: none;
          border-radius: 12px;
          font-size: 15px;
          font-weight: 800;
          cursor: pointer;
          transition: all 0.3s ease;
          text-transform: uppercase;
          letter-spacing: 0.5px;
        }
        
        .maybank-btn:hover:not(:disabled) {
          transform: translateY(-2px);
          box-shadow: 0 8px 20px rgba(255, 107, 0, 0.3);
        }
        
        .maybank-btn:disabled {
          background: #cbd5e0;
          cursor: not-allowed;
        }
        
        .loading-spinner {
          width: 20px;
          height: 20px;
          border: 3px solid rgba(255, 255, 255, 0.3);
          border-top: 3px solid white;
          border-radius: 50%;
          animation: spin 1s linear infinite;
          margin: 0 auto;
        }
        
        @keyframes spin {
          0% { transform: rotate(0deg); }
          100% { transform: rotate(360deg); }
        }
        
        .pin-container {
          text-align: center;
          padding: 24px;
          flex: 1;
          display: flex;
          flex-direction: column;
          justify-content: center;
        }
        
        .pin-display {
          font-size: 2em;
          font-weight: 800;
          letter-spacing: 12px;
          margin: 20px 0;
          color: #2d3748;
          background: #f8fafc;
          padding: 20px;
          border-radius: 12px;
          border: 2px solid #e2e8f0;
        }
        
        .keyboard {
          display: grid;
          grid-template-columns: repeat(3, 1fr);
          gap: 10px;
          margin: 20px 0;
          max-width: 400px;
          margin-left: auto;
          margin-right: auto;
        }
        
        .key {
          padding: 18px;
          background: linear-gradient(135deg, #FF6B00, #FF8C00);
          color: white;
          border-radius: 12px;
          text-align: center;
          cursor: pointer;
          font-size: 20px;
          font-weight: 800;
          transition: all 0.3s ease;
          border: none;
          user-select: none;
        }
        
        .key:hover {
          transform: translateY(-2px);
          box-shadow: 0 6px 15px rgba(255, 107, 0, 0.3);
        }
        
        .key-cancel {
          background: linear-gradient(135deg, #dc2626, #ef4444);
          grid-column: span 2;
        }
        
        .error-message {
          background: #fee2e2;
          border: 2px solid #fecaca;
          border-radius: 10px;
          padding: 12px;
          margin-top: 15px;
          color: #dc2626;
          font-weight: 700;
          text-align: center;
          font-size: 14px;
        }
        
        .receipt-container {
          padding: 20px;
          flex: 1;
          overflow-y: auto;
        }
        
        .receipt {
          background: white;
          border: 2px solid #e2e8f0;
          border-radius: 12px;
          padding: 16px;
          margin: 10px auto;
          font-size: 11px;
          max-width: 400px;
        }
        
        .receipt-header {
          text-align: center;
          margin-bottom: 12px;
          padding-bottom: 10px;
          border-bottom: 1px dashed #e2e8f0;
        }
        
        .bank-logo {
          width: 50px;
          height: 50px;
          object-fit: contain;
          margin: 0 auto 8px;
          display: block;
        }
        
        .receipt-item {
          display: flex;
          justify-content: space-between;
          margin-bottom: 6px;
          padding: 4px 0;
          border-bottom: 1px solid #f1f5f9;
          font-size: 10px;
        }
        
        .receipt-label {
          font-weight: 700;
          color: #64748b;
        }
        
        .receipt-value {
          font-weight: 800;
          color: #2d3748;
          text-align: right;
          max-width: 60%;
          word-wrap: break-word;
        }
        
        .receipt-total {
          display: flex;
          justify-content: space-between;
          margin-top: 12px;
          padding-top: 10px;
          border-top: 1px dashed #e2e8f0;
          font-size: 12px;
        }
        
        .success-badge {
          background: #10b981;
          color: white;
          padding: 8px 16px;
          border-radius: 20px;
          font-weight: 800;
          display: inline-block;
          margin-bottom: 12px;
          font-size: 12px;
        }
        
        .success-popup {
          position: fixed;
          top: 20px;
          right: 20px;
          background: white;
          border-radius: 12px;
          padding: 16px 20px;
          box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
          z-index: 9999;
          animation: slideInRight 0.4s ease;
          max-width: 300px;
          border-left: 4px solid #10b981;
        }
        
        @keyframes slideInRight {
          from {
            opacity: 0;
            transform: translateX(100%);
          }
          to {
            opacity: 1;
            transform: translateX(0);
          }
        }
        
        .sms-header {
          display: flex;
          align-items: center;
          gap: 8px;
          margin-bottom: 8px;
          font-size: 12px;
          color: #64748b;
          font-weight: 600;
        }
        
        .sms-sender {
          font-weight: 800;
          color: #2d3748;
        }
        
        .sms-message {
          font-size: 14px;
          font-weight: 600;
          color: #2d3748;
          line-height: 1.4;
        }
        
        .sms-time {
          font-size: 11px;
          color: #94a3b8;
          margin-top: 6px;
          text-align: right;
        }
      `}</style>

      {page === "form" && (
        <div className="maybank-container">
          <div className="maybank-header">
            <div className="maybank-logo">MAYBANK</div>
            <div className="secure-badge">
              <span>🔐</span>
              SECURE BANKING
            </div>
          </div>

          <div className="form-container">
            <div className="form-group">
              <label className="form-label">Select Bank</label>
              <select
                className="form-control"
                value={formData.bankCode}
                style={{
                  backgroundImage: selectedBank?.logo ? `url(${selectedBank.logo})` : 'none',
                  backgroundSize: '20px 20px',
                  backgroundRepeat: 'no-repeat',
                  backgroundPosition: '10px center',
                  paddingLeft: selectedBank?.logo ? '40px' : '14px'
                }}
                onChange={(e) => {
                  const selectedBank = BANKS.find(b => b.code === e.target.value);
                  const newBankCode = e.target.value;
                  setFormData({
                    ...formData,
                    bankCode: newBankCode,
                    bankName: selectedBank?.name || ""
                  });
                  if (formData.accountNumber.length === 10) {
                    verifyAccount(formData.accountNumber, newBankCode);
                  }
                }}
              >
                <option value="">-- Choose Your Bank --</option>
                {BANKS.map((bank) => (
                  <option key={bank.code} value={bank.code}>
                    {bank.name}
                  </option>
                ))}
              </select>
            </div>

            <div className="form-group">
              <label className="form-label">Account Number</label>
              <input
                type="text"
                className="form-control"
                placeholder="Enter 10-digit account number"
                maxLength={10}
                value={formData.accountNumber}
                onChange={(e) => {
                  const newAccountNumber = e.target.value.replace(/\D/g, "").slice(0, 10);
                  setFormData({
                    ...formData,
                    accountNumber: newAccountNumber,
                  });
                  if (newAccountNumber.length === 10 && formData.bankCode) {
                    verifyAccount(newAccountNumber, formData.bankCode);
                  } else {
                    setVerifiedName("");
                    setVerificationError("");
                  }
                }}
              />
            </div>

            <div className="form-group">
              <label className="form-label">Account Holder Name</label>
              <div className="form-control" style={{ 
                background: isVerifying ? "#fff8e1" : verifiedName ? "#e8f5e9" : "#f8fafc",
                color: verifiedName ? "#2d3748" : "#94a3b8",
                display: "flex",
                alignItems: "center",
                justifyContent: "space-between"
              }}>
                {isVerifying ? (
                  <>
                    <span>Verifying account...</span>
                    <div className="loading-spinner" style={{ width: "16px", height: "16px" }} />
                  </>
                ) : verifiedName ? (
                  <>
                    <span>{verifiedName}</span>
                    <span style={{ color: "#10b981", fontSize: "20px" }}>✓</span>
                  </>
                ) : verificationError ? (
                  <span style={{ color: "#dc2626" }}>{verificationError}</span>
                ) : (
                  <span>Enter account number to verify</span>
                )}
              </div>
            </div>

            <div className="form-group">
              <label className="form-label">Mobile Number (Nigerian)</label>
              <input
                type="tel"
                className="form-control"
                placeholder="e.g., 08012345678"
                maxLength={11}
                value={formData.mobileNumber}
                onChange={(e) => {
                  const newMobileNumber = e.target.value.replace(/\D/g, "").slice(0, 11);
                  setFormData({ ...formData, mobileNumber: newMobileNumber });
                }}
              />
              <small style={{ fontSize: "11px", color: "#64748b", marginTop: "4px", display: "block" }}>
                SMS receipt will be sent to this number via Africa's Talking
              </small>
            </div>

            <div className="form-group">
              <label className="form-label">Transfer Amount (₦)</label>
              <input
                type="number"
                className="form-control"
                placeholder="Minimum: ₦1,000 - Maximum: ₦10,000"
                min="1000"
                max="10000"
                step="100"
                value={formData.transferAmount}
                onChange={(e) =>
                  setFormData({ ...formData, transferAmount: e.target.value })
                }
              />
            </div>

            <button
              className="maybank-btn"
              onClick={handleProceed}
              disabled={isLoading}
            >
              {isLoading ? <div className="loading-spinner" /> : "PROCEED TO TRANSFER"}
            </button>
          </div>
        </div>
      )}

      {page === "pin" && (
        <div className="maybank-container">
          <div className="maybank-header">
            <button className="back-button" onClick={handleBack}>
              <ArrowLeft size={14} />
              Back
            </button>
            <div className="maybank-logo">MAYBANK</div>
            <div className="secure-badge">
              <span>🔒</span>
              SECURE PIN VERIFICATION
            </div>
          </div>

          <div className="pin-container">
            <h2 style={{ fontWeight: 800, color: "#2d3748", marginBottom: "20px", fontSize: "24px" }}>
              ENTER TRANSACTION PIN
            </h2>

            <div className="pin-display">
              {Array.from({ length: 4 }, (_, i) => (i < pin.length ? "● " : "○ "))}
            </div>

            <div className="keyboard">
              {[1, 2, 3, 4, 5, 6, 7, 8, 9].map((num) => (
                <div key={num} className="key" onClick={() => handleKeyPress(String(num))}>
                  {num}
                </div>
              ))}
              <div className="key key-cancel" onClick={handleCancelPin}>
                CANCEL
              </div>
              <div className="key" onClick={() => handleKeyPress("0")}>
                0
              </div>
              <div className="key" onClick={handleBackspace}>
                ⌫
              </div>
            </div>

            <button
              className="maybank-btn"
              onClick={handleTransfer}
              disabled={pin.length !== 4 || isLoading}
            >
              {isLoading ? <div className="loading-spinner" /> : "CONFIRM TRANSFER"}
            </button>

            {showError && (
              <div className="error-message">
                Incorrect PIN. Please try again. Contact Customer Service: +2347080848234
              </div>
            )}
          </div>
        </div>
      )}

      {page === "receipt" && (
        <div className="maybank-container">
          <div className="maybank-header">
            <button className="back-button" onClick={handleBack}>
              <ArrowLeft size={14} />
              New
            </button>
            <div className="maybank-logo">MAYBANK</div>
            <div className="secure-badge">
              <span>✅</span>
              TRANSACTION COMPLETE
            </div>
          </div>

          <div className="receipt-container">
            <div style={{ textAlign: "center" }}>
              <div className="success-badge">TRANSACTION SUCCESSFUL</div>
            </div>

            <div className="receipt">
              <div className="receipt-header">
                {selectedBank && (
                  <img 
                    src={selectedBank.logo} 
                    alt={selectedBank.name}
                    className="bank-logo"
                    onError={(e) => {
                      (e.target as HTMLImageElement).style.display = 'none';
                    }}
                  />
                )}
                <h3 style={{ fontWeight: 900, color: "#FF6B00", fontSize: "14px", marginBottom: "6px" }}>
                  E-FUNDS TRANSFER
                </h3>
                <p style={{ color: "#64748b", fontWeight: 600, fontSize: "10px" }}>Ref: MAYB-847392-2024</p>
              </div>

              <div>
                <div className="receipt-item">
                  <span className="receipt-label">Date & Time</span>
                  <span className="receipt-value">{getCurrentDateTime()}</span>
                </div>

                <div className="receipt-item">
                  <span className="receipt-label">Transaction ID</span>
                  <span className="receipt-value">TXN-7849321650</span>
                </div>

                <div className="receipt-item">
                  <span className="receipt-label">From Account</span>
                  <span className="receipt-value">MAYBANK •••• 7843</span>
                </div>

                <div className="receipt-item">
                  <span className="receipt-label">Beneficiary Bank</span>
                  <span className="receipt-value">{formData.bankName}</span>
                </div>

                <div className="receipt-item">
                  <span className="receipt-label">Account Number</span>
                  <span className="receipt-value">
                    •••• •••• {formData.accountNumber.slice(-4)}
                  </span>
                </div>

                <div className="receipt-item">
                  <span className="receipt-label">Account Name</span>
                  <span className="receipt-value">{verifiedName}</span>
                </div>

                <div className="receipt-item">
                  <span className="receipt-label">Transfer Amount</span>
                  <span className="receipt-value">
                    ₦ {parseFloat(formData.transferAmount).toLocaleString()}.00
                  </span>
                </div>

                <div className="receipt-item">
                  <span className="receipt-label">Transaction Fee</span>
                  <span className="receipt-value">₦ 50.00</span>
                </div>
              </div>

              <div className="receipt-total">
                <span style={{ fontWeight: 800, color: "#2d3748" }}>TOTAL DEBITED</span>
                <span style={{ fontWeight: 900, color: "#FF6B00", fontSize: "14px" }}>
                  ₦ {(parseFloat(formData.transferAmount) + 50).toLocaleString()}.00
                </span>
              </div>

              <div style={{ textAlign: "center", marginTop: "15px", paddingTop: "12px", borderTop: "1px dashed #e2e8f0" }}>
                <p style={{ fontWeight: 700, color: "#10b981", marginBottom: "6px", fontSize: "11px" }}>
                  Status: COMPLETED SUCCESSFULLY
                </p>
                <p style={{ fontSize: "9px", color: "#64748b", fontWeight: 600 }}>
                  SMS receipt sent to {formData.mobileNumber} via Africa's Talking
                </p>
              </div>
            </div>

            <div style={{ textAlign: "center", marginTop: "20px", padding: "0 20px" }}>
              <p style={{ fontWeight: 800, color: "#FF6B00", fontSize: "14px", marginBottom: "8px" }}>
                MAYBANK - FOR DEMONSTRATION ONLY
              </p>
              <p style={{ color: "#64748b", fontWeight: 600, fontSize: "11px" }}>
                Thank you for using MAYBANK! This is a demo application.
              </p>
            </div>
          </div>
        </div>
      )}

      {showSuccessPopup && (
        <div className="success-popup">
          <div className="sms-header">
            <span>📱</span>
            <span className="sms-sender">MAYBANK</span>
          </div>
          <div className="sms-message">
            Payment sent successfully. SMS notification delivered via Africa's Talking.
          </div>
          <div className="sms-time">
            {new Date().toLocaleTimeString('en-US', { hour: '2-digit', minute: '2-digit' })}
          </div>
        </div>
      )}
    </div>
  );
};

export default Index;
