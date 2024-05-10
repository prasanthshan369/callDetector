import { useParams } from "react-router-dom";
import { loadStripe } from "@stripe/stripe-js";
import {
  PaymentElement,
  useElements,
  Elements,
  useStripe,
} from "@stripe/react-stripe-js";

import { useState, useEffect } from "react";
import PaymentForm from "../components/PaymentForm";
const stripePromise = loadStripe(
  "pk_test_51LKw5cSCpIasVmwfViT4xdzoVhiDkjn1Sf8WJfpoX2v5p3cBBTNG3svMOFJ5oUD6kdyNId9X2aHnHNRrdWXIj3yU00jawknFMm"
);
const Payment = () => {
  const { id } = useParams();
  const options = {
    clientSecret: id,
    apperarance: {
      theme: "stripe",
    },
  };

  return (
    <div className="flex justify-center items-center h-[100vh] w-[100vw]">
      {id && (
        <Elements options={options} stripe={stripePromise}>
          <PaymentForm secretKey={id} />
        </Elements>
      )}
    </div>
  );
};

export default Payment;

//form details
import React, { useState, useEffect } from "react";
import {
  PaymentElement, useElements,Elements, useStripe,} from "@stripe/react-stripe-js";

const PaymentForm = ({ secretKey }) => {
  const [clientSecret, setClientSecret] = useState();
  const [errorMessage, setErrorMessage] = useState(false);
  const [message, setMessage] = useState(null);
  const [isLoading, setIsLoading] = useState(false);
  const elements = useElements();
  const stripe = useStripe();
  useEffect(() => {
    setClientSecret(secretKey);
  }, [secretKey]);
  useEffect(() => {
    if (!stripe) {
      return;
    }
    if (!clientSecret) {
      return;
    }
    stripe.retrievePaymentIntent(clientSecret).then(({ paymentIntent }) => {
      switch (paymentIntent.status) {
        case "succeeded":
          setMessage("Payment succeeded !");
          break;
        case "processing":
          setMessage("Your payment is processing !");
          setErrorMessage(true);
          break;
        case "requires_payment_method":
          setMessage("your Payment was not successfuly, please try again !");
          setErrorMessage(false);
          break;
        default:
          setErrorMessage(true);
          setMessage("Something went wrong");
          break;
      }
    });
  }, [stripe, clientSecret]);
  const handleSubmit = async (e) => {
    e.preventDefault();
    if (!stripe || !elements) {
      return;
    }
    setIsLoading(true);
    const { error } = await stripe.confirmPayment({
      elements,
      confirmParams: {
        return_url: "http://localhost:3000",
      },
    });
    if (error.type === "card_error" || error.type === "validation_error") {
      setMessage(error.message);
    } else {
      setMessage("An unexpected error occured");
    }
    setIsLoading(false);
  };
  return (
    <form className="w-[30vw] min-w-[500px] shadow rounded-md p-[40px]">
      <PaymentElement className="mb-10" />
      <button
        onClick={handleSubmit}
        className="payment-button w-full bg-blue-950 py-2 rounded-md"
        disabled={isLoading || !stripe || !elements}
      >
        <span className=" py-2 text-white">
          {isLoading ? <div id="spinner"></div> : "Pay Now"}
        </span>
      </button>
      {errorMessage && message && (
        <div className="text-blue-950 text-base pt-10 text-center">
          {message}
        </div>
      )}
    </form>
  );
};

export default PaymentForm;

