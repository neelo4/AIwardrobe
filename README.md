import React from 'react';
import { screen, fireEvent } from '@testing-library/react';
import '@testing-library/jest-dom/extend-expect';
import { render } from '../../../test-utils/renderWithProviders';
import FaqSection from './FaqSection';
import { useNavigate } from 'react-router-dom';

// Mock the dependencies
jest.mock('react-router-dom', () => ({
  useNavigate: jest.fn(),
}));

const mockTtData = {
  payments: [
    {
      question: "How do I make changes to my payment details?",
      answer: "1. Go to the $t(pages.contactUsUplift.internalLinks.policies) page or the $t(pages.contactUsUplift.internalLinks.home) page\n2. Click View Policy\n3. Then you can either:\n\t1. Click Edit under Payment Method to change account, OR\n\t2. Click Edit under Payment Plan to edit payment date or frequency.",
    },
    {
      question: "How do I make a BPAY payment?",
      answer: "You can make a BPAY payment by calling your bank or using internet banking and selecting the BPAY option.",
    },
  ],
  claims: [
    {
      question: "How long do I have to start a claim?",
      answer: "You should make a claim as soon as possible, but if you're unsure get in touch with us.",
    },
    {
      question: "How do I check the status of my car or home claim?",
      answer: "You can check the progress of a car or home claim by going to Claims.",
    },
  ],
};

// Mock the tt function at the module level
jest.mock('@iag-common/iag-brand-context', () => ({
  ...jest.requireActual('@iag-common/iag-brand-context'),
  tt: jest.fn(() => mockTtData),
}));

describe('FaqSection', () => {
  beforeEach(() => {
    jest.clearAllMocks();
  });

  test('renders FAQ sections for payments and claims', () => {
    render(<FaqSection />);
    expect(screen.getByText('Payments')).toBeInTheDocument();
    expect(screen.getByText('Claims')).toBeInTheDocument();
  });

  test('renders correct number of FAQ items for payments', () => {
    render(<FaqSection />);
    const paymentQuestions = screen.getAllByText(/How do I make/);
    expect(paymentQuestions).toHaveLength(2);
  });

  test('renders correct number of FAQ items for claims', () => {
    render(<FaqSection />);
    const claimQuestions = screen.getAllByText(/How (long|do)/);
    expect(claimQuestions).toHaveLength(2);
  });

  test('expands and collapses FAQ items when clicked', () => {
    render(<FaqSection />);
    const firstPaymentQuestion = screen.getByText("How do I make changes to my payment details?");
    
    // Initially, the answer should not be visible
    expect(screen.queryByText(/1. Go to the/)).not.toBeInTheDocument();

    // Click to expand
    fireEvent.click(firstPaymentQuestion);
    expect(screen.getByText(/1. Go to the/)).toBeInTheDocument();

    // Click to collapse
    fireEvent.click(firstPaymentQuestion);
    expect(screen.queryByText(/1. Go to the/)).not.toBeInTheDocument();
  });

  test('navigates to internal links when clicked', () => {
    const mockNavigate = jest.fn();
    useNavigate.mockReturnValue(mockNavigate);

    render(<FaqSection />);
    const firstPaymentQuestion = screen.getByText("How do I make changes to my payment details?");
    fireEvent.click(firstPaymentQuestion);

    const internalLink = screen.getByText('$t(pages.contactUsUplift.internalLinks.policies)');
    fireEvent.click(internalLink);

    expect(mockNavigate).toHaveBeenCalled();
  });
});
