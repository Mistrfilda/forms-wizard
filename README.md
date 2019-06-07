# nette/forms wizard
[![Build Status](https://travis-ci.org/contributte/wizard.svg?branch=master)](https://travis-ci.org/contributte/wizard)

## Installation

**Composer**
```
composer require contributte/forms-wizard
```

**Config**

```yaml
extensions:
    - Contributte\FormWizard\DI\WizardExtension
```

# Usage

## Component

```php

use Nette\Application\UI\Form;

class Wizard extends Contributte\FormWizard\Wizard {

    protected function finish(): void 
    {
        $values = $this->getValues();
    }

    protected function createStep1(): Form 
    {
        $form = $this->createForm();

        $form->addText('name', 'User name')
            ->setRequired();

        $form->addSubmit(self::NEXT_SUBMIT_NAME, 'Next');

        return $form;
    }

    protected function createStep2(): Form 
    {
        $form = $this->createForm();

        $form->addText('email', 'Email')
            ->setRequired();

        $form->addSubmit(self::PREV_SUBMIT_NAME, 'Back');
        $form->addSubmit(self::FINISH_SUBMIT_NAME, 'Register');

        return $form;
    }
}
```

```yaml
services:
    - Wizard
```

## Presenter

```php

final class HomepagePresenter extends Nette\Application\UI\Presenter {

    /** @var Wizard @inject */
    public $wizard;
    
    public function handleChangeStep(int $step): void 
    {    
        $this['wizard']->setStep($step);
        
        $this->redirect('wizard'); // Optional, hides parameter from URL
    }

    protected function createComponentWizard(): Wizard 
    {
        return $this->wizard;
    }

}

```

## Template

```html
<div n:wizard="wizard">
    <ul n:if="!$wizard->isSuccess()">
        <li n:foreach="$wizard->steps as $step" n:class="$wizard->isDisabled($step) ? disabled, $wizard->isActive($step) ? active">
            <a n:tag-if="$wizard->useLink($step)" n:href="changeStep! $step">{$step}</a>
        </li>
    </ul>

    {step 1}
        {control $form}
    {/step}

    {step 2}
        {control $form}
    {/step}

    {step success}
        Registration was successful
    {/step}
</div>
```
