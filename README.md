# Icon Shim Plugin

A "shim" plugin that allows plugins make use of the core `<l:icon>` tag, while still remaining backward compatible with
older versions of Jenkins core that do not have the icon tag.

## Step 1 - Add the Shim Plugin as a Plugin dependency

Simply add the following to your maven pom.xml depedencies (or equivalent for other build descriptor):

```
<dependency>
  <groupId>org.jenkins-ci.plugins.icon-shim</groupId>
  <artifactId>icon-shim</artifactId>
  <version>1.0.2</version>
</dependency>
```

## Step 2 - Add Plugin icons to the IconSet

You need to define an IconSet to allow the shim properly fallback to rendering old-style `<img>`
based icons when the plugin is installed on a Jenkins version that doesn't have the core `<l:icon>` layout tag.

A good place to do this is in the Plugin's `Plugin` implementation class.  If the Plugin doesn't already have a
`Plugin` implementation, just add one as follows (e.g. for the Credentials Plugin). Jenkins will find and use it:

```
public class PluginImpl extends Plugin {

    @Override
    public void start() throws Exception {
        super.start();

        // Register the small (16x16) icons...
        IconSet.icons.addIcon(new Icon("icon-credentials-credential icon-sm", "credentials/images/16x16/credential.png", Icon.ICON_SMALL_STYLE, IconType.PLUGIN));
        IconSet.icons.addIcon(new Icon("icon-credentials-credentials icon-sm", "credentials/images/16x16/credentials.png", Icon.ICON_SMALL_STYLE, IconType.PLUGIN));
        IconSet.icons.addIcon(new Icon("icon-credentials-domain icon-sm", "credentials/images/16x16/domain.png", Icon.ICON_SMALL_STYLE, IconType.PLUGIN));
        IconSet.icons.addIcon(new Icon("icon-credentials-new-credential icon-sm", "credentials/images/16x16/new-credential.png", Icon.ICON_SMALL_STYLE, IconType.PLUGIN));
        IconSet.icons.addIcon(new Icon("icon-credentials-new-domain icon-sm", "credentials/images/16x16/new-domain.png", Icon.ICON_SMALL_STYLE, IconType.PLUGIN));

        // Register the medium (24x24) icons...
        IconSet.icons.addIcon(new Icon("icon-credentials-credential icon-md", "credentials/images/24x24/credential.png", Icon.ICON_MEDIUM_STYLE, IconType.PLUGIN));
        IconSet.icons.addIcon(new Icon("icon-credentials-credentials icon-md", "credentials/images/24x24/credentials.png", Icon.ICON_MEDIUM_STYLE, IconType.PLUGIN));
        IconSet.icons.addIcon(new Icon("icon-credentials-domain icon-md", "credentials/images/24x24/domain.png", Icon.ICON_MEDIUM_STYLE, IconType.PLUGIN));
        IconSet.icons.addIcon(new Icon("icon-credentials-new-credential icon-md", "credentials/images/24x24/new-credential.png", Icon.ICON_MEDIUM_STYLE, IconType.PLUGIN));
        IconSet.icons.addIcon(new Icon("icon-credentials-new-domain icon-md", "credentials/images/24x24/new-domain.png", Icon.ICON_MEDIUM_STYLE, IconType.PLUGIN));

        // Register the large (32x32) icons...
        IconSet.icons.addIcon(new Icon("icon-credentials-credential icon-lg", "credentials/images/32x32/credential.png", Icon.ICON_LARGE_STYLE, IconType.PLUGIN));
        IconSet.icons.addIcon(new Icon("icon-credentials-credentials icon-lg", "credentials/images/32x32/credentials.png", Icon.ICON_LARGE_STYLE, IconType.PLUGIN));
        IconSet.icons.addIcon(new Icon("icon-credentials-domain icon-lg", "credentials/images/32x32/domain.png", Icon.ICON_LARGE_STYLE, IconType.PLUGIN));
        IconSet.icons.addIcon(new Icon("icon-credentials-new-credential icon-lg", "credentials/images/32x32/new-credential.png", Icon.ICON_LARGE_STYLE, IconType.PLUGIN));
        IconSet.icons.addIcon(new Icon("icon-credentials-new-domain icon-lg", "credentials/images/32x32/new-domain.png", Icon.ICON_LARGE_STYLE, IconType.PLUGIN));

        // Register the x-large (48x48) icons...
        IconSet.icons.addIcon(new Icon("icon-credentials-credential icon-xlg", "credentials/images/48x48/credential.png", Icon.ICON_XLARGE_STYLE, IconType.PLUGIN));
        IconSet.icons.addIcon(new Icon("icon-credentials-credentials icon-xlg", "credentials/images/48x48/credentials.png", Icon.ICON_XLARGE_STYLE, IconType.PLUGIN));
        IconSet.icons.addIcon(new Icon("icon-credentials-domain icon-xlg", "credentials/images/48x48/domain.png", Icon.ICON_XLARGE_STYLE, IconType.PLUGIN));
        IconSet.icons.addIcon(new Icon("icon-credentials-new-credential icon-xlg", "credentials/images/48x48/new-credential.png", Icon.ICON_XLARGE_STYLE, IconType.PLUGIN));
        IconSet.icons.addIcon(new Icon("icon-credentials-new-domain icon-xlg", "credentials/images/48x48/new-domain.png", Icon.ICON_XLARGE_STYLE, IconType.PLUGIN));
    }
}
```

## Step 3 - Replace icon `<img>` markup with `<lsi:icon>` shim markup

Your plugin will have been rendering icons as follows:

```
<img src="${resURL}/plugin/credentials/images/${iconSize}/domain.png"
     alt="${safeDescription}" tooltip="${safeDescription}" class="icon${iconSize}"/>
```

Simply replace this `<img>` based icon markup with Icon Shim markup as follows:

```
<lsi:icon class="icon-credentials-domain" iconSize="${iconSize}" alt="${safeDescription}"
    tooltip="${safeDescription}" xmlns:lsi="/shim/layout/icon" />
```

The Icon Shim tag will figure out if the underlying Jenkins instance has the core `<l:icon>` layout tag, or not.  After
making that evaluation it will either render an old-style `<img>` icon, or it will delegate to the core `<l:icon>` layout
tag to let it render the icon.  Of course, the shim tag will also translate the old icon size tokens ('16x16',
'24x24', '32x32' and '48x48') to their new equivalent class specs ('icon-sm', 'icon-md', 'icon-lg' and 'icon-xlg').