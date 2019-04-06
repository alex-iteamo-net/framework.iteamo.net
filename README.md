================================================================================
Flexible lightweight framework based on "apps" concept.
You will find some originality in apps inheritance opportunities and techniques.
Here the apps can inherit almost everything from parent apps: 
templates, js, css, datafiles, classes, helpers any used in app file.
This opens up the possibility for flexible customization of written applications.
---
Additional functional of classes-apps is based on the use of traits 
and auxiliary libraries implementing certain system interfaces.
---
For example in this code you will have 3 apps.
The templator, translator libs and dataFile helper will be taken
from framework. But you can easily change any of them. 
For example you want to have you own dataFile helper. 
All you need is just place /root/helpers/dataFile.php and write you code.
All your apps will use it. If you want overwrite this functional only
in test and common apps you should place it in /common/helpers/dataFile.php
---
/* file /root/app.php */
namespace root;
$objIteamo = require($_SERVER['DOCUMENT_ROOT'] . '/iteamo/init.php');
class app extends iteamo\framework\app {
  use \iteamo\framework\traits\withInheritance;
  use \iteamo\framework\traits\withServices;
}
---
/* file /common/app.php */
namespace common;
class app extends \root\app {
  use \iteamo\framework\traits\withTemplator;
  use \iteamo\framework\traits\withTranslator;
  //
  public function __construct() {   
    parent::__construct();    
    $this->setService('templator', $this->getObject('libs/templators/php')->setParent($this));  
    $this->setService('translator', $this->getObject('libs/localization/translator', [
      'defaultLanguage' => 'en',
      'arLanguages' => ['en', 'ru'],
      'directory' => $this->getPath() . '/translations', 
    ]));            
    $this->setService('placeholders', $this->getObject('libs/placeholders'));  
  }  
}
---
/* file /test/app.php */
namespace test;
class app extends \common\app {
  public function getResponse() {
    $strResponse = '';  
    $strResponse = $this->parseTemplate('templates/index.html', 
      $arData = $this->getHelper('dataFile')->read($this->getPath('data/data.php')));  
    $strResponse = $this->getService('placeholders')->replace($strResponse); 
    return $strResponse; 
  }
}
---
You can control whether to use inheritance or not.
================================================================================
