import { BrandDataProps, connectIagBrand } from '@iag-common/iag-brand-context';
import { Accordion, AccordionItem } from '@iag/chroma-react-ui.components';
import React, { useEffect } from 'react';
import { useNavigate } from 'react-router-dom';

interface FaqItem {
  question: string;
  answer: string;
}

const FaqSection: React.FC<BrandDataProps> = ({ t, tt }) => {
  const [isAccordionExpanded, setIsAccordionExpanded] = React.useState(false);
  const navigate = useNavigate();
  const baseKey = `pages.contactUsUplift`;

  const fetchCategories = () => {
    return tt(`${baseKey}.categories`);
  };
  const faqData = fetchCategories();

  useEffect(() => {
    if (!isAccordionExpanded) return;
    document.querySelectorAll('#contactus-internal-link').forEach((a) => {
      a.addEventListener('click', (event) => {
        event.preventDefault();
        const href = a.getAttribute('href');
        navigate(href);
      });
    });
  }, [isAccordionExpanded]);

  return (
    <Accordion id="contactUsTable" className="pv-mb-g">
      {Object.entries(faqData).map(([category, questions], categoryIndex) => (
        <React.Fragment key={`category-${categoryIndex}`}>
          <h2 className="text-2xl font-bold mb-4 mt-8 pb-2">{category.charAt(0).toUpperCase() + category.slice(1)}</h2>
          {Array.isArray(questions) &&
            questions.map((item: FaqItem, questionIndex: number) => (
              <AccordionItem
                key={`faq-item-${category}-${questionIndex}`}
                id={`item-${category}-${questionIndex}`}
                button={<>{item.question}</>}
                content={t(item?.answer)}
                onShow={() => setIsAccordionExpanded(true)}
                onHide={() => setIsAccordionExpanded(false)}
              />
            ))}
        </React.Fragment>
      ))}
    </Accordion>
  );
};

export default connectIagBrand()(FaqSection);
