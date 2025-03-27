<template>
  <div>
    <div class='header navbar'>
      <div class="db-header-container">
        <div class="db-diagram-name-container">
          <span id='diagramName' style="width:250px; overflow: hidden; text-overflow: ellipsis; white-space: nowrap;"
            class="db-diagram-name" @click='renameDiagram'>
            Untitled Diagram
          </span>
          <input id='diagramEditable' type="text" class="db-diagram-name" @keydown='diagramNameKeyDown' @focusout='diagramNameChange' />
          <span id='diagramreport' class="db-diagram-name db-save-text"></span>
        </div>

        <div class='db-menu-container'>
          <div class="menu-control">
            <ejs-menu :items='dropDownDataSources.fileMenuItems' @select='menuSelect'></ejs-menu>
          </div>
        </div>
      </div>

      <div class='db-toolbar-editor'>
        <div class='db-toolbar-container'>
          <ejs-toolbar overflowMode='Scrollable' id="toolbarEditor" @clicked="toolbarClick">
            <e-items>
              <!-- Toolbar Items -->
              <e-item prefixIcon="sf-icon-save tb-icons" tooltipText="Save Diagram"
                cssClass="tb-item-start tb-item-save"></e-item>
              <e-item prefixIcon="sf-icon-open tb-icons" tooltipText="Open Diagram"
                cssClass="tb-item-middle tb-item-open"></e-item>
              <e-item prefixIcon="sf-icon-print tb-icons" tooltipText="Print Diagram"
                cssClass="tb-item-middle tb-item-print"></e-item>
              <e-item prefixIcon="sf-icon-export tb-icons" tooltipText="Export Diagram"
                cssClass="tb-item-end tb-item-export"></e-item>
              <e-item type='Separator'></e-item>
              <e-item prefixIcon='sf-icon-undo tb-icons' tooltipText='Undo'
                cssClass='tb-item-start tb-item-undo'></e-item>
              <e-item prefixIcon='sf-icon-redo tb-icons' tooltipText='Redo'
                cssClass='tb-item-end tb-item-redo'></e-item>
              <e-item type='Separator'></e-item>

              <e-item tooltipText='Font Family' :template="'fontFamilyTemplate'"
                cssClass='tb-item-start item-singleSelect'>

              </e-item>
              <template v-slot:fontFamilyTemplate>
                <div style="margin-top: 5px;">
                  <ejs-dropdownlist id="fontFamily" :dataSource="dropDownDataSources.fontFamilyList"
                    v-model="fontFamilyValue" @change="fontFamilyChange"></ejs-dropdownlist>
                </div>
              </template>

              <e-item tooltipText='Font Size' :template="'fontSizeTemplate'" cssClass='tb-item-end item-singleSelect'>

              </e-item>
              <template v-slot:fontSizeTemplate>
                <div style="margin-top: 5px;">
                  <ejs-dropdownlist id="fontSize" :dataSource="dropDownDataSources.fontSizeList" v-model="fontSizeValue"
                    width="60px" @change="fontSizeChange"></ejs-dropdownlist>
                </div>
              </template>

              <e-item type='Separator'></e-item>
              <e-item prefixIcon='sf-icon-bold tb-icons' tooltipText='Bold'
                cssClass='tb-item-start item-singleSelect'></e-item>
              <e-item prefixIcon='sf-icon-italic tb-icons' tooltipText='Italic'
                cssClass='tb-item-middle item-singleSelect'></e-item>
              <e-item prefixIcon='sf-icon-underline tb-icons' tooltipText='Underline'
                cssClass='tb-item-end item-singleSelect'></e-item>
              <e-item type='Separator'></e-item>

              <e-item prefixIcon='sf-icon-font-color tb-icons' :template="'templateColor'" tooltipText='Font Color'
                cssClass='tb-item-start item-singleSelect'>
              </e-item>
              <template v-slot:templateColor>
                <div style="margin-top: 5px;">
                  <ejs-colorpicker id="font-color-picker" class="color-font"
                    @change="fontColorChange"></ejs-colorpicker>
                </div>
              </template>

              <e-item prefixIcon="sf-icon-fill tb-icons" :template="'templateFillColor'" tooltipText="Fill Color"
                cssClass="tb-item-middle item-singleSelect">
              </e-item>
              <template v-slot:templateFillColor>
                <div style="margin-top: 5px;">
                  <ejs-colorpicker id="fill-color-picker" class="color-fill"
                    @change="fillColorChange"></ejs-colorpicker>
                </div>
              </template>
              <e-item prefixIcon='sf-icon-fill tb-icons' tooltipText='Stroke Color' :template="'templateStrokeColor'"
                cssClass='tb-item-end item-singleSelect'>
                <template v-slot:templateStrokeColor>
                  <div style="margin-top: 5px;">
                    <ejs-colorpicker id="stroke-color-picker" type="colorpicker" class='color-stroke'
                      @change="strokeColorChange" />
                  </div>
                </template>
              </e-item>

              <e-item type='Separator'></e-item>
              <e-item prefixIcon='sf-icon-pointer' tooltipText='Select Tool'
                cssClass='tb-item-start tb-item-selected'></e-item>
              <e-item prefixIcon='sf-icon-pan' tooltipText='Pan Tool' cssClass='tb-item-middle'></e-item>
              <e-item prefixIcon='db-overview' tooltipText='Overview' cssClass='tb-item-end db-overview'></e-item>

              <e-item type='Separator'></e-item>


            </e-items>
          </ejs-toolbar>
        </div>
      </div>
    </div>
    <div>
      <div class="db-diagram-container" id="">
        <div id="diagramContainerDiv" class='db-current-diagram-container'>
          <ejs-diagram ref="diagramRef" id="diagram" :width="width" :height="height" :layout="layout"
            :dataSourceSettings="data" :rulerSettings="rulerSettings" :snapSettings="snapSettings"
            :getNodeDefaults='nodeDefaults' :getConnectorDefaults='connDefaults' :selectionChange="selectionChange"
            :setNodeTemplate='setNodeTemplate' :created="created"  :collectionChange="collectionChange" :selectedItems="selectedItems"
            :scrollSettings="scrollSettings" :scrollChange="scrollChange" :historyChange="historyChange"
            :pageSettings="pageSettings" :onUserHandleMouseDown="onUserHandleMouseDown">
          </ejs-diagram>
        </div>
      </div>
      <div class='db-property-editor-container' id="propertyPanel"
        style="overflow:auto;border-left: 1px solid #CBCBCB;border-top: 1px solid #CBCBCB;height:800px;">
        <div id="generalDiagramContainer" class="db-general-diagram-prop-container" style="padding-bottom: -200px;">
          <div id='diagramPropertyContainer' class="db-diagram-prop-container">
            <div class="row db-prop-header-text" style="margin-left: 15px;margin-top: 10px;">
              Properties
            </div>

            <div class="row property-panel-content" id="appearance">
              <!-- Import -->
              <div class="row" style="padding-top: 10px">

                <!-- Orientation -->
                <div class="row row-header" style="padding-top: 10px">
                  Orientation
                </div>
                <div id="orientation">
                  <div class="row" style="padding-top: 8px">
                    <div class="image-pattern-style2 e-selected-orientation-style" id="TopToBottom" @click="handleOrientationClick"
                      style="background-image: url(https://ej2.syncfusion.com/javascript/demos/src/diagram/Images/common-orientation/toptobottom.png); margin-right: 3px">
                    </div>
                    <div class="image-pattern-style2" id="BottomToTop" @click="handleOrientationClick"
                      style="background-image: url(https://ej2.syncfusion.com/javascript/demos/src/diagram/Images/common-orientation/bottomtotop.png); margin: 0px 3px">
                    </div>
                    <div class="image-pattern-style2" id="LeftToRight" @click="handleOrientationClick"
                      style="background-image: url(https://ej2.syncfusion.com/javascript/demos/src/diagram/Images/common-orientation/lefttoright.png); margin: 0px 3px">
                    </div>
                  </div>
                  <div class="row" style="padding-top: 8px">
                    <div class="image-pattern-style2" id="RightToLeft" @click="handleOrientationClick"
                      style="background-image: url(https://ej2.syncfusion.com/javascript/demos/src/diagram/Images/common-orientation/righttoleft.png); margin: 0px 3px">
                    </div>
                  </div>
                </div>

                <!-- Subtree Alignment -->
                <div class="row row-header" style="padding-top: 10px">
                  Subtree Alignment
                </div>
                <div id="pattern">
                  <div class="row" style="padding-top: 8px">
                    <div class="image-pattern-style" id="pattern1" @click="subTreeClick"
                      style="background-image: url(https://ej2.syncfusion.com/javascript/demos/src/diagram/patternimages/Pattern_1.png); margin-right: 3px">
                    </div>
                    <div class="image-pattern-style" id="pattern2" @click="subTreeClick"
                      style="background-image: url(https://ej2.syncfusion.com/javascript/demos/src/diagram/patternimages/Pattern_2.png); margin: 0px 3px">
                    </div>
                    <div class="image-pattern-style e-selected-pattern-style" id="pattern5" @click="subTreeClick"
                      style="background-image: url(https://ej2.syncfusion.com/javascript/demos/src/diagram/patternimages/Pattern_5.png); margin: 0px 3px">
                    </div>
                  </div>
                  <div class="row" style="padding-top: 8px">
                    <div class="image-pattern-style" id="pattern6" @click="subTreeClick"
                      style="background-image: url(https://ej2.syncfusion.com/javascript/demos/src/diagram/patternimages/Pattern_6.png); margin: 0px 3px">
                    </div>
                    <div class="image-pattern-style" id="pattern7" @click="subTreeClick"
                      style="background-image: url(https://ej2.syncfusion.com/javascript/demos/src/diagram/patternimages/Pattern_7.png); margin: 0px 3px">
                    </div>
                    <div class="image-pattern-style" id="pattern8" @click="subTreeClick"
                      style="background-image: url(https://ej2.syncfusion.com/javascript/demos/src/diagram/patternimages/Pattern_8.png); margin: 0px 3px">
                    </div>
                  </div>
                  <div class="row" style="padding-top: 8px">
                    <div class="image-pattern-style" id="pattern9" @click="subTreeClick"
                      style="background-image: url(https://ej2.syncfusion.com/javascript/demos/src/diagram/patternimages/Pattern_9.png); margin: 0px 3px">
                    </div>
                  </div>
                </div>
              </div>

              <!-- Spacing -->
              <div class="row" style="padding-top: 10px">
                <div class="row row-header">
                  Behavior
                </div>
                <div class="row" style="padding-top: 8px;">
                  <div class="row db-prop-row">
                    <div class="col-xs-8 db-col-left" style="width:120px">
                      <span class="db-prop-text-style">Horizontal Spacing</span>
                    </div>
                    <div class="col-xs-4 db-col-right" style="margin-left: 6px;margin-bottom: 10px;">
                      <div class="db-text-container" style="width: 77px; margin-left: 10px;">
                        <div class="db-text-input">
                          <ejs-numerictextbox id="horizontalSpacing" :min="20" :max="100" :step=2 width="77px"
                            style="width: 77px;" format="##.##" v-model="horizontalSpacing"
                            @change='onhSpacingChange'></ejs-numerictextbox>
                        </div>
                      </div>
                    </div>
                  </div>
                  <div class="row db-prop-row">
                    <div class="col-xs-8 db-col-left" style="width:120px">
                      <span class="db-prop-text-style">Vertical Spacing</span>
                    </div>
                    <div class="col-xs-4 db-col-right" style="margin-left: 6px;margin-bottom: 10px;">
                      <div class="db-text-container" style="width: 77px; margin-left: 10px;">
                        <div class="db-text-input">
                          <ejs-numerictextbox id="verticalSpacing" :min="30" :max="100" :step=2 width="77px"
                            style="width: 77px;" format="##.##" v-model="verticalSpacing"
                            @change='onvSpacingChange'></ejs-numerictextbox>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
              </div>

              <div class="row db-prop-row" style="padding-top: 10px;">
                <ejs-button id="reLayoutBtn" className="e-control e-btn e-primary" cssclass='e-flat e-db-primary' @click="reLayout">Re-Layout</ejs-button>
                <ejs-button id="addAssistantBtn" iconCss="sf-icon-add-child" className="e-control e-btn e-primary" cssclass='e-flat e-db-primary' aria-disabled="true"
                  @click="addAssistant" style="opacity: 0.5; pointer-events: none !important;">Add-Assistant</ejs-button>
              </div>
              <div class="row db-prop-row"
                :style="{ borderTop: '1px solid #CBCBCB', paddingTop: '10px', paddingBottom: '10px' }">
                <div class="col-xs-4 db-col-left" :style="{ width: '160px', marginLeft: '5px' }">
                  <ejs-dropdownbutton id="pictureDropdown" :iconCss="'sf-icon-insert_image'" content="Picture"
                    :width="'100px'" :items="dropDownDataSources.pictureItems"
                    @select="insertOrRemovePicture"></ejs-dropdownbutton>
                </div>
                <div class="col-xs-6 db-col-right" :style="{ width: '160px' }">
                  <ejs-dropdownbutton id="shapeTemplatesDropDown" :iconCss="'e-icons e-properties-2'"
                    content="Templates" :width="'100px'" :height="'100px'" :items="dropDownDataSources.templateItems"
                    @select="modifyNodeTemplate"></ejs-dropdownbutton>
                </div>
              </div>
              <div class="row db-prop-row"
                :style="{ borderTop: '1px solid #CBCBCB', paddingTop: '10px' }">
                <div class="col-xs-4 db-col-left" style="width: 60%; margin-bottom: 5px;">
                  <ejs-multiselect id="multiCheckbox" :value="multiCheckboxValue"
                    :dataSource="dropDownDataSources.fieldsMultiListItems" :close-on-select="false"
                    :clear-on-select="false" :placeholder="'Select fields'" :popupHeight="'300px'" :mode="'CheckBox'"
                    :fields='dropDownDataSources.checkBoxFields' @open="onOpen" :show-labels="true" :filterable="true"
                    :track-by="'id'" :taggable="true" @change="checkboxmodifyNodeTemplate"></ejs-multiselect>
                </div>
                <div class="col-xs-4 db-col-right" style="margin-left: 10px;">
                  <ejs-button id="searchBtn" cssclass='db-search-btn' className="e-control e-btn e-primary" iconCss='e-icons e-search' @click="searchBtn"
                    style="font-size: 10px;">
                     Search
                  </ejs-button>
                </div>
              </div>

              <div class="row db-prop-row" style="border-top: 1px solid #CBCBCB; padding-top: 10px; margin-top: 10px;">
                <ejs-checkbox id="expandable" ref="expandableRef" label="Expandable" @change="onExpandChange"></ejs-checkbox>
              </div>

              <div class="row db-prop-row">
                <div class="col-xs-2 db-col-right db-prop-text-style" style="padding-top: 6px; margin-left: 10px;">
                  <span class="db-prop-text-style">Zoom</span>
                </div>
                <div class="col-xs-6 db-col-left" style="padding-right: 10px;">
                  <ejs-slider id='zoomSlider' type='MinRange' :min="30" :max="300" v-model="zoomLevel" @change="updateZoomLevel"
                    ></ejs-slider>
                </div>
                <div class="col-xs-2 db-col-right">
                  <input id="zoomSliderText" type="text" v-model="zoomLevel" readOnly class="db-readonly-input" />
                </div>
                <div class="col-lg-4" id="overview-container" :style="{
                  zIndex: -1,
                  width: '25%',
                  height: '350px',
                  padding: '0px',
                  right: '5px',
                  bottom: '5px',
                  background: '#f7f7f7',
                  position: 'relative',
                }">
                  <button class="overview-toggle-btn" id="toggleOverviewButton" @click="toggleOverviewButton">
                    &#215;
                  </button>
                  <ejs-overview id="overview" width="100%" :sourceID="'diagram'"></ejs-overview>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="uploadDefault">
        <ejs-uploader ref="defaultUploadRef" id="defaultfileupload" :asyncSettings="path" @success="onUploadSuccess"
          @failure="onUploadFailure" @progress="onUploadFileSelected"></ejs-uploader>
      </div>

      <div class="pictureUpload">
        <ejs-uploader ref="pictureUploadRef" id="pictureUpload" :showFileList="false" :asyncSettings="path"
          @success="onPictureUploadSuccess"></ejs-uploader>
      </div>

      <div class="dialog-container">
        <ejs-dialog ref="dialogRef" id="defaultDialog" :header="header" :animationSettings="animationSettings"
          :showCloseIcon="showCloseIcon" :target="target" width="500px" :visible="visible" is-modal="true"
          :buttons="defaultButtons">
          <div id="editNodeDialogContent">
            <div class="row">
              Name
            </div>
            <div class="row db-dialog-child-prop-row">
              <input type="text" id="name" v-model="name" />
            </div>
            <br>
            <div class="row">
              Role
            </div>
            <div class="row db-dialog-child-prop-row">
              <input type="text" id="role" v-model="role" />
            </div>
            <br>
            <div class="row">
              Employee Id
            </div>
            <div class="row db-dialog-child-prop-row">
              <input type="text" id="empId" v-model="empId" />
            </div>
            <br>
            <div class="row">
              Team
            </div>
            <div class="row db-dialog-child-prop-row">
              <input type="text" id="team" v-model="team" />
            </div>
            <br>
            <div class="row">
              E-mail
            </div>
            <div class="row db-dialog-child-prop-row">
              <input type="text" id="mail" v-model="mail" />
            </div>
            <br>
            <div class="row">
              Phone Number
            </div>
            <div class="row db-dialog-child-prop-row">
              <input type="text" id="phNumber" v-model="phNumber" />
            </div>
            <br>
          </div>
        </ejs-dialog>
        <div class="exportDialog">
        <ejs-dialog ref="exportDialogRef" id="exportDialog" :header="exportHeader"
          :animationSettings="animationSettings" :showCloseIcon="showCloseIcon" :target="target" width="500px"
          :visible="visible" is-modal="true" :buttons="exportingButtons">

          <div id="exportDialogContent">
            <div class="row">
              <div class="row">
                File Name
              </div>
              <div class="row db-dialog-child-prop-row">
                <input type="text" id="exportfileName"  v-model="fileName" />
              </div>
            </div>
            <div class="row db-dialog-prop-row">
              <div class="col-xs-6 db-col-left">
                <div class="row">
                  Format
                </div>
                <div class="row db-dialog-child-prop-row">
                  <ejs-dropdownlist id="exportFormat" :dataSource="dropDownDataSources.fileFormats"
                    v-model="formatValue"></ejs-dropdownlist>
                </div>
              </div>
              <div class="col-xs-6 db-col-right">
                <div class="row">
                  Region
                </div>
                <div class="row db-dialog-child-prop-row">
                  <ejs-dropdownlist id="exportRegion" :dataSource="dropDownDataSources.diagramRegions"
                    v-model="regionsValue"></ejs-dropdownlist>
                </div>
              </div>
            </div>
          </div>

        </ejs-dialog>
      </div>
        <ejs-dialog ref="searchDialog" id="searchDialog" :header="searchHeader" :animationSettings="animationSettings"
          :showCloseIcon="showCloseIcon" :target="searchTarget" :width=400 :position="position" :visible="visible"
          :isModal="false" :buttons="searchButtons">
            <div id="searchDialogContent">
              <div class="row">
                <div class="row">Search By</div>
                <div class="row db-dialog-child-prop-row">
                  <ejs-dropdownlist id="searchDropDown" :fields="fields" :index="0"
                    :dataSource="dropDownDataSources.searchDropDownItems"></ejs-dropdownlist>
                </div>
                <div class="row">Search Value</div>
                <div class="row db-dialog-child-prop-row">
                  <input type="text" id="searchBox" />
                </div>
              </div>
            </div>
        </ejs-dialog>
      </div>
    </div>
  </div>
</template>

<script>
import { onMounted, ref, computed, defineComponent } from "vue";
import {
  DiagramComponent, OverviewComponent, DataBinding, HierarchicalTree, SnapConstraints, NodeConstraints, ConnectorConstraints, StackPanel, PathElement, ImageElement, TextElement, SelectorConstraints,
  PrintAndExport, Snapping, UndoRedo
} from "@syncfusion/ej2-vue-diagrams";
import { MenuComponent, ToolbarComponent, ItemsDirective, ItemDirective } from "@syncfusion/ej2-vue-navigations";
import { NumericTextBoxComponent, SliderComponent, ColorPickerComponent, UploaderComponent } from "@syncfusion/ej2-vue-inputs";
import { ButtonComponent } from "@syncfusion/ej2-vue-buttons";
import { CheckBoxComponent } from "@syncfusion/ej2-vue-buttons";
import { DropDownListComponent, MultiSelectComponent, CheckBoxSelection } from "@syncfusion/ej2-vue-dropdowns";
import { DialogComponent } from "@syncfusion/ej2-vue-popups";
import { DropDownButtonComponent } from "@syncfusion/ej2-vue-splitbuttons";
import { provide } from "vue";
import { DataManager } from "@syncfusion/ej2-data";
import { DiagramClientSideEvents } from './app/script/events';
import { UtilityMethods } from './app/script/utilitymethod';
import { DropDownDataSources } from './app/script/dropdowndatasource';

const handles = [
  {
    name: 'Add New Child', pathData: 'M13.55896,0L18.461914,0 18.461914,13.557983 32,13.557983 32,18.481018 18.5,18.481018 18.5,32 13.55896,32 13.55896,18.481018 0,18.481018 0,13.557983 13.55896,13.557983z',
    offset: 1, side: 'Bottom', tooltip: { content: 'Add New Child', position: 'BottomRight' }
  },
  {
    name: 'Edit Fields', pathData: 'M19.312381,27.48482L18.503085,29.661004 20.944314,29.115917z M24.540007,21.633355L20.390685,25.734296 22.789237,28.131621 26.97936,24.028763z M27.175001,19.029084L25.962399,20.227547 28.408086,22.629793 29.616994,21.446061z M10.602995,15C5.8599977,15 1.999999,18.829 1.999999,23.536 1.999999,24.895 3.1159983,26 4.4899979,26L17.731456,26 17.992104,25.299033 18.172095,25.116051 18.171058,25.116051 23.266144,20.080393 23.156942,19.84575C22.697614,18.897562 22.06124,18.03375 21.28199,17.31 19.682991,15.82 17.592992,15 15.397993,15L12.999995,15z M12.999995,2C9.9420033,2 7.45401,4.467 7.45401,7.5 7.45401,9.947 9.1090055,12.123 11.478999,12.791 12.461997,13.068 13.535994,13.069 14.521991,12.791 16.891984,12.122 18.54598,9.9459996 18.54598,7.5 18.54598,4.467 16.057987,2 12.999995,2z M12.999995,0C17.160984,0 20.545975,3.3639994 20.545975,7.5 20.545975,9.6899061 19.575261,11.720467 17.995872,13.115179L17.808254,13.274325 17.879796,13.290484C19.650854,13.712344 21.290927,14.584437 22.645989,15.846 23.445364,16.590375 24.124036,17.454672 24.656125,18.399828L24.764509,18.599506 26.399983,16.983108C26.614001,16.772111 26.895004,16.666856 27.175756,16.666978 27.456508,16.6671 27.737007,16.772599 27.950018,16.983108L31.969938,20.961079 31.999967,21.450059C31.999967,21.74208,31.88095,22.027051,31.672944,22.233074L23.04003,30.684984 22.706048,30.770982 17.325118,31.973977C17.245102,31.991006 17.165085,31.999978 17.085069,31.999978 16.769093,31.999978 16.464103,31.863993 16.252069,31.619976 15.987118,31.314985 15.91113,30.89 16.054073,30.511035L16.987776,28 4.4899979,28C2.013999,28 0,25.997 0,23.536 0,18.633812 3.3858614,14.50334 7.9561033,13.332249L8.1918802,13.274945 8.0051279,13.116582C6.4251604,11.721779 5.4540157,9.6905622 5.4540157,7.5 5.4540157,3.3639994 8.8390064,0 12.999995,0z',
    visible: true, offset: 1, side: 'Top', margin: { top: 0, bottom: 0, left: 0, right: 0 }, tooltip: { content: 'Edit Fields', position: 'BottomRight' }
  },
];
const imagePath = require('@/assets/images/alex.png');
let diagramInstance;
let utilityMethods;
let clientSideEvents;
let defaultUploaderInstance;
let pictureUploadInstance;
let expandableInstance;
let item = [
  {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": "parent", "Name": "Maria Anders", "Designation": "Managing Director",
      "IsExpand": "true", "RatingColor": "#C34444", "ImageUrl": require('@/assets/images/maria.png'), "EmployeeID": 'SYNC1001', "Team": "TypeScript", "EmailId": 'maria.anders@gmail.com', "PhoneNumber": '0324 - 1819301'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 1, "Name": "Ana Trujillo", "Designation": "Project Manager",
      "IsExpand": "false",
      "RatingColor": "#68C2DE", "ReportingPerson": "parent", "ImageUrl": require('@/assets/images/carlos.png'), "EmployeeID": 'SYNC1002', "Team": "Java", "EmailId": 'ana.truj@gmail.com', "PhoneNumber": '0324 - 1819302'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 2, "Name": "Anto Moreno", "Designation": "Project Lead",
      "IsExpand": "false",
      "RatingColor": "#93B85A", "ReportingPerson": 1, "ImageUrl": require('@/assets/images/daniel.png'), "EmployeeID": 'SYNC1003', "Team": "Windows", "EmailId": 'ana.moreno@gmail.com', "PhoneNumber": '0324 - 1819303'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 3, "Name": "Thomas Hardy", "Designation": "Senior S/w Engg",
      "IsExpand": "false",
      "RatingColor": "#68C2DE", "ReportingPerson": 2, "ImageUrl": require('@/assets/images/jaime.png'), "EmployeeID": 'SYNC1004', "Team": "UX", "EmailId": 'thomos.hardy@gmail.com', "PhoneNumber": '0324 - 1819304'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 4, "Name": "Christina kaff", "Designation": "S/w Engg",
      "IsExpand": "false",
      "RatingColor": "#93B85A", "ReportingPerson": 3, "ImageUrl": require('@/assets/images/felipe.png'), "EmployeeID": 'SYNC1005', "Team": "UX", "EmailId": 'chris.kaff@gmail.com', "PhoneNumber": '0324 - 1819305'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 5, "Name": "Hanna Moos", "Designation": "Project Trainee",
      "IsExpand": "true",
      "RatingColor": "#D46E89", "ReportingPerson": 4, "ImageUrl": require('@/assets/images/helen.png'), "EmployeeID": 'SYNC1006', "Team": "Windows", "EmailId": 'hanna.moos@gmail.com', "PhoneNumber": '0324 - 1819306'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 6, "Name": "Peter Citeaux", "Designation": "S/w Engg",
      "IsExpand": "true",
      "RatingColor": "#68C2DE", "ReportingPerson": 5, "ImageUrl": require('@/assets/images/rene.png'), "EmployeeID": 'SYNC1007', "Team": "Java", "EmailId": 'peter.cite@gmail.com', "PhoneNumber": '0324 - 1819307'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 7, "Name": "Martín Kloss", "Designation": "Project Trainee",
      "IsExpand": "false",
      "RatingColor": "#93B85A", "ReportingPerson": 6, "ImageUrl": require('@/assets/images/yoshi.png'), "EmployeeID": 'SYNC1008', "Team": "UX", "EmailId": 'martin.kloss@gmail.com', "PhoneNumber": '0324 - 1819308'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 8, "Name": "Elizabeth Mary", "Designation": "Project Trainee",
      "IsExpand": "None",
      "RatingColor": "#93B85A", "ReportingPerson": 6, "ImageUrl": require('@/assets/images/yvonne.png'), "EmployeeID": 'SYNC1009', "Team": "Java", "EmailId": 'elizabeth.marys@gmail.com', "PhoneNumber": '0324 - 1819309'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 9, "Name": "Victoria Ash", "Designation": "Senior S/w Engg",
      "IsExpand": "None",
      "RatingColor": "#D46E89", "ReportingPerson": 5, "ImageUrl": require('@/assets/images/carlos.png'), "EmployeeID": 'SYNC1010', "Team": "React", "EmailId": 'victoria.ash@gmail.com', "PhoneNumber": '0324 - 1819310'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 10, "Name": "Francisco Yang", "Designation": "Senior S/w Engg",
      "IsExpand": "None",
      "RatingColor": "#93B85A", "ReportingPerson": 3, "ImageUrl": require('@/assets/images/maria.png'), "EmployeeID": 'SYNC1011', "Team": "Java", "EmailId": 'francisco.yang@gmail.com', "PhoneNumber": '0324 - 1819311'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 17, "Name": "Ann Devon", "Designation": "Project Manager",
      "IsExpand": "false",
      "RatingColor": "#68C2DE", "ReportingPerson": 25, "ImageUrl": require('@/assets/images/yoshi.png'), "EmployeeID": 'SYNC1012', "Team": "UX", "EmailId": 'Ann.devon@gmail.com', "PhoneNumber": '0324 - 1819312'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 18, "Name": "Roland Mendel", "Designation": "Project Lead",
      "IsExpand": "true",
      "RatingColor": "#68C2DE", "ReportingPerson": 17, "ImageUrl": require('@/assets/images/carlos.png'), "EmployeeID": 'SYNC1013', "Team": "UX", "EmailId": 'roland.mendel@gmail.com', "PhoneNumber": '0324 - 1819313'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 19, "Name": "Aria Cruz", "Designation": "Senior S/w Engg",
      "IsExpand": "false",
      "RatingColor": "#93B85A", "ReportingPerson": 18, "ImageUrl": require('@/assets/images/daniel.png'), "EmployeeID": 'SYNC1014', "Team": "Angular", "EmailId": 'aria.cruz@gmail.com', "PhoneNumber": '0324 - 1819314'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 20, "Name": "Martine Rancé", "Designation": "S/w Engg",
      "IsExpand": "None",
      "RatingColor": "#93B85A", "ReportingPerson": 18, "ImageUrl": require('@/assets/images/helen.png'), "EmployeeID": 'SYNC1015', "Team": "UX", "EmailId": 'martina.rance@gmail.com', "PhoneNumber": '0324 - 1819315'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 21, "Name": "Maria Larsson", "Designation": "Project Trainee",
      "IsExpand": "false",
      "RatingColor": "#EBB92E", "ReportingPerson": 19, "ImageUrl": require('@/assets/images/maria.png'), "EmployeeID": 'SYNC1016', "Team": "UX", "EmailId": 'maria.larsson@gmail.com', "PhoneNumber": '0324 - 1819316'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 22, "Name": "Diego Roel", "Designation": "Project Trainee",
      "IsExpand": "false",
      "RatingColor": "#D46E89", "ReportingPerson": 21, "ImageUrl": require('@/assets/images/jaime.png'), "EmployeeID": 'SYNC1017', "Team": "TypeScript", "EmailId": 'diego.roel@gmail.com', "PhoneNumber": '0324 - 1819317'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 23, "Name": "Peter Franken", "Designation": "Project Trainee",
      "IsExpand": "None",
      "RatingColor": "#D46E89", "ReportingPerson": 21, "ImageUrl": require('@/assets/images/felipe.png'), "EmployeeID": 'SYNC1018', "Team": "JavaScript", "EmailId": 'peter.franken@gmail.com', "PhoneNumber": '0324 - 1819318'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 25, "Name": "Carine Schmitt", "Designation": "Project Manager",
      "IsExpand": "None",
      "RatingColor": "#EBB92E", "ReportingPerson": "parent", "ImageUrl": require('@/assets/images/maria.png'), "EmployeeID": 'SYNC1019', "Team": "Java", "EmailId": 'carine.schmit@gmail.com', "PhoneNumber": '0324 - 1819319'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 26, "Name": "Paolo Accorti", "Designation": "Project Lead",
      "IsExpand": "None",
      "RatingColor": "#D46E89", "ReportingPerson": 36, "ImageUrl": require('@/assets/images/yvonne.png'), "EmployeeID": 'SYNC1020', "Team": "React", "EmailId": 'paolo.acc@gmail.com', "PhoneNumber": '0324 - 1819320'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 27, "Name": "Eduardo Roel", "Designation": "Senior S/w Engg",
      "IsExpand": "true",
      "RatingColor": "#93B85A", "ReportingPerson": 26, "ImageUrl": require('@/assets/images/sergio.png'), "EmployeeID": 'SYNC1021', "Team": "JavaScript", "EmailId": 'eduardo.roel@gmail.com', "PhoneNumber": '0324 - 1819321'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 28, "Name": "José Pedro", "Designation": "Senior S/w Engg",
      "IsExpand": "true",
      "RatingColor": "#D46E89", "ReportingPerson": 27, "ImageUrl": require('@/assets/images/yoshi.png'), "EmployeeID": 'SYNC1022', "Team": "Java", "EmailId": 'josé.pedro@gmail.com', "PhoneNumber": '0324 - 1819322'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 29, "Name": "André Fonseca", "Designation": "Senior S/w Engg",
      "IsExpand": "true",
      "RatingColor": "#EBB92E", "ReportingPerson": 28, "ImageUrl": require('@/assets/images/joseph.png'), "EmployeeID": 'SYNC1023', "Team": "React", "EmailId": 'andré.fonseca@gmail.com', "PhoneNumber": '0324 - 1819323'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 30, "Name": "Howard Snyd", "Designation": "S/w Engg",
      "IsExpand": "false",
      "RatingColor": "#68C2DE", "ReportingPerson": 29, "ImageUrl": require('@/assets/images/simon.png'), "EmployeeID": 'SYNC1024', "Team": "JavaScript", "EmailId": 'howard.synd@gmail.com', "PhoneNumber": '0324 - 1819324'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 31, "Name": "Manu Pereira", "Designation": "Project Trainee",
      "IsExpand": "None",
      "RatingColor": "#D46E89", "ReportingPerson": 29, "ImageUrl": require('@/assets/images/maria.png'), "EmployeeID": 'SYNC1025', "Team": "JavaScript", "EmailId": 'manu.periera@gmail.com', "PhoneNumber": '0324 - 1819325'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 32, "Name": "Mario Pontes", "Designation": "S/w Engg",
      "IsExpand": "None",
      "RatingColor": "#D46E89", "ReportingPerson": 29, "ImageUrl": require('@/assets/images/annette.png'), "EmployeeID": 'SYNC1026', "Team": "Java", "EmailId": 'mario.pontes@gmail.com', "PhoneNumber": '0324 - 1819326'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 33, "Name": "Carlos Schmitt", "Designation": "Project Trainee",
      "IsExpand": "None",
      "RatingColor": "#D46E89", "ReportingPerson": 29, "ImageUrl": require('@/assets/images/maria.png'), "EmployeeID": 'SYNC1027', "Team": "React", "EmailId": 'carlos.schmitt@gmail.com', "PhoneNumber": '0324 - 1819327'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 34, "Name": "Yoshi Latimer", "Designation": "Project Trainee",
      "IsExpand": "true",
      "RatingColor": "#D46E89", "ReportingPerson": 29, "ImageUrl": require('@/assets/images/daniel.png'), "EmployeeID": 'SYNC1028', "Team": "React", "EmailId": 'yoshi.latimer@gmail.com', "PhoneNumber": '0324 - 1819328'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 35, "Name": "Patricia Kenna", "Designation": "Project Trainee",
      "IsExpand": "true",
      "RatingColor": "#EBB92E", "ReportingPerson": 29, "ImageUrl": require('@/assets/images/helen.png'), "EmployeeID": 'SYNC1029', "Team": "JavaScript", "EmailId": 'patricia.kenna@gmail.com', "PhoneNumber": '0324 - 1819329'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 36, "Name": "Helen Bennett", "Designation": "Project Lead",
      "IsExpand": "None",
      "RatingColor": "#D46E89", "ReportingPerson": 25, "ImageUrl": require('@/assets/images/rene.png'), "EmployeeID": 'SYNC1030', "Team": "Java", "EmailId": 'helen.bennette@gmail.com', "PhoneNumber": '0324 - 1819330'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 65, "Name": "Alej Camino", "Designation": "Project Manager",
      "IsExpand": "false",
      "RatingColor": "#93B85A", "ReportingPerson": "parent", "ImageUrl": require('@/assets/images/carlos.png'), "EmployeeID": 'SYNC1031', "Team": "Windows", "EmailId": 'aleg.camino@gmail.com', "PhoneNumber": '0324 - 1819331'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 66, "Name": "Jonas Bergsen", "Designation": "Project Lead",
      "IsExpand": "None",
      "RatingColor": "#68C2DE", "ReportingPerson": 65, "ImageUrl": require('@/assets/images/joseph.png'), "EmployeeID": 'SYNC1032', "Team": "JavaScript", "EmailId": 'jonas.bergsen@gmail.com', "PhoneNumber": '0324 - 1819332'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 67, "Name": "Jose Pavarotti", "Designation": "Project Trainee",
      "IsExpand": "None",
      "RatingColor": "#D46E89", "ReportingPerson": 68, "ImageUrl": require('@/assets/images/maria.png'), "EmployeeID": 'SYNC1033', "Team": "Windows", "EmailId": 'jose.pavarotti@gmail.com', "PhoneNumber": '0324 - 1819333'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 68, "Name": "Miguel Angel", "Designation": "Senior S/w Engg",
      "IsExpand": "None",
      "RatingColor": "#D46E89", "ReportingPerson": 66, "ImageUrl": require('@/assets/images/yoshi.png'), "EmployeeID": 'SYNC1034', "Team": "Angular", "EmailId": 'miguel.angel@gmail.com', "PhoneNumber": '0324 - 1819334'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 69, "Name": "Jytte Petersen", "Designation": "Senior S/w Engg",
      "IsExpand": "true",
      "RatingColor": "#68C2DE", "ReportingPerson": 68, "ImageUrl": require('@/assets/images/felipe.png'), "EmployeeID": 'SYNC1035', "Team": "Angular", "EmailId": 'jytte.petersen@gmail.com', "PhoneNumber": '0324 - 1819335'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 70, "Name": "Kloss Perrier", "Designation": "Project Lead",
      "IsExpand": "None",
      "RatingColor": "#93B85A", "ReportingPerson": 72, "ImageUrl": require('@/assets/images/jytte.png'), "EmployeeID": 'SYNC1036', "Team": "JavaScript", "EmailId": 'closs.perrier@gmail.com', "PhoneNumber": '0324 - 1819336'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 71, "Name": "Art Nancy", "Designation": "Senior S/w Engg",
      "IsExpand": "true",
      "RatingColor": "#D46E89", "ReportingPerson": 27, "ImageUrl": require('@/assets/images/rene.png'), "EmployeeID": 'SYNC1037', "Team": "Java", "EmailId": 'art.nancy@gmail.com', "PhoneNumber": '0324 - 1819337'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 72, "Name": "Pascal Cartrain", "Designation": "Project Lead",
      "IsExpand": "true",
      "RatingColor": "#EBB92E", "ReportingPerson": 65, "ImageUrl": require('@/assets/images/renete.png'), "EmployeeID": 'SYNC1038', "Team": "Vue", "EmailId": 'pascal.cartrain@gmail.com', "PhoneNumber": '0324 - 1819338'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 73, "Name": "Liz Nixon", "Designation": "Senior S/w Engg",
      "IsExpand": "false",
      "RatingColor": "#68C2DE", "ReportingPerson": 68, "ImageUrl": require('@/assets/images/maria.png'), "EmployeeID": 'SYNC1039', "Team": "JavaScript", "EmailId": 'liz.nixon@gmail.com', "PhoneNumber": '0324 - 1819339'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 89, "Name": "Georg Pipps", "Designation": "Senior S/w Engg",
      "IsExpand": "None",
      "RatingColor": "#EBB92E", "ReportingPerson": "parent", "ImageUrl": require('@/assets/images/rene.png'), "EmployeeID": 'SYNC1040', "Team": "Java", "EmailId": 'georg.pipps@gmail.com', "PhoneNumber": '0324 - 1819340'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 30, "Name": "Isabel Castro", "Designation": "Project Trainee",
      "IsExpand": "None",
      "RatingColor": "#D46E89", "ReportingPerson": 89, "ImageUrl": require('@/assets/images/jhon.png'), "EmployeeID": 'SYNC1041', "Team": "Windows", "EmailId": 'isabel.castro@gmail.com', "PhoneNumber": '0324 - 1819341'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 90, "Name": "Rene Phillips", "Designation": "Project Trainee",
      "IsExpand": "false",
      "RatingColor": "#68C2DE", "ReportingPerson": 89, "ImageUrl": require('@/assets/images/jytte.png'), "EmployeeID": 'SYNC1042', "Team": "JavaScript", "EmailId": 'rene.phillips@gmail.com', "PhoneNumber": '0324 - 1819342'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 91, "Name": "Lúcia Carvalho", "Designation": "Project Trainee",
      "IsExpand": "None",
      "RatingColor": "#93B85A", "ReportingPerson": 89, "ImageUrl": require('@/assets/images/yoshi.png'), "EmployeeID": 'SYNC1043', "Team": "Java", "EmailId": 'lúcia.carvalho@gmail.com', "PhoneNumber": '0324 - 1819343'
    }, {
      "Fill": "white", "StrokeColor": "black", "FontFamily": "Arial", "IsBold": false, "IsItalic": false, "Decoration": "None", "FontSize": 12, "color": "black", "Id": 92, "Name": "Horst Kloss", "Designation": "Project Trainee",
      "IsExpand": "None",
      "RatingColor": "#D46E89", "ReportingPerson": 89, "ImageUrl": require('@/assets/images/helen.png'), "EmployeeID": 'SYNC1044', "Team": "Angular", "EmailId": 'horst.kloss@gmail.com', "PhoneNumber": '0324 - 1819344'
    },
];


export default defineComponent({
  name: "App",
  components: {
    'ejs-diagram': DiagramComponent,
    "ejs-overview": OverviewComponent,
    'ejs-dropdownbutton': DropDownButtonComponent,
    "ejs-toolbar": ToolbarComponent,
    "e-items": ItemsDirective,
    "e-item": ItemDirective,
    'ejs-dropdownlist': DropDownListComponent,
    "ejs-checkbox": CheckBoxComponent,
    "ejs-colorpicker": ColorPickerComponent,
    "ejs-dialog": DialogComponent,
    'ejs-uploader': UploaderComponent,
    "ejs-numerictextbox": NumericTextBoxComponent,
    'ejs-slider': SliderComponent,
    "ejs-menu": MenuComponent,
    "ejs-button": ButtonComponent,
    'ejs-multiselect': MultiSelectComponent,
  },

  setup() {
    // const utilityMethods = new UtilityMethods();
    const dropDownDataSources = new DropDownDataSources();
    const width = "100%";
    const height = "815px";
    const layout = {
      type: 'OrganizationalChart',
      getLayoutInfo: (node, options) => {
        if (!options.hasSubTree) {
          options.type = 'Right';
        }
      }
    };
    const path = {
      saveUrl: 'https://services.syncfusion.com/vue/production/api/FileUploader/Save',
      removeUrl: 'https://services.syncfusion.com/vue/production/api/FileUploader/Remove'
    };
    const position = { X: 'right', Y: 'center' };
    const visible = false;
    //Data to load layout
    const data = {
      //sets the fields to bind
      id: 'Id', parentId: 'ReportingPerson',
      dataSource: new DataManager(item),
    };
    //Ruler settings to enable ruler
    const rulerSettings = {
      showRulers: true
    };
    //Snap Settings to disable diagram graph
    const snapSettings = {
      constraints: SnapConstraints.None & ~(SnapConstraints.ShowLines) & ~(SnapConstraints.SnapToLines)
    };
    //Nodedefaults to customize node
    const nodeDefaults = (obj) => {
      obj.height = 50;
      obj.addInfo = { fill: 'white' }
      obj.constraints = NodeConstraints.Default & ~NodeConstraints.Rotate | NodeConstraints.Tooltip;
      obj.tooltip = { content: getContent(obj.data), position: 'BottomRight', relativeMode: 'Object' },
        obj.style = { fill: 'transparent', strokeWidth: 2 };
      var expandIconShape = 'None';
      var collapseIconShape = 'None';
      if (expandableInstance && expandableInstance.checked) {
        expandIconShape = 'Minus';
        collapseIconShape = 'Plus';
      } else {
        expandIconShape = 'None';
        collapseIconShape = 'None';
      }
      obj.expandIcon = {
        height: 20,
        width: 20,
        iconColor: 'white',
        cornerRadius: 10,
        borderColor: 'black',
        shape:expandIconShape,
        fill: 'black',
        offset: { x: 0.5, y: 1.2 },
        pathData: 'M16.261993,32L16.359985,31.934998 16.454987,32 16.48999,31.846008 32,20.705013 32,12.254999 16.359985,23.539014 0,12.254999 0,20.705013 15.77301,31.846008z'
      };
      obj.collapseIcon = {
        height: 20,
        width: 20,
        iconColor: 'white',
        cornerRadius: 10,
        borderColor: 'black',
        shape: collapseIconShape,
        fill: 'black',
        offset: { x: 0.5, y: 1.2 },
        pathData: 'M16.261993,0L16.359985,0.065002445 16.454987,0 16.48999,0.15399169 32,11.294986 32,19.745 16.359985,8.5149861 0,19.745 0,11.294986 16.22699,0.15399169z'
      };
      return obj;
    };
    
    // To get the tooltip content of node.
    function getContent(obj) {
      var tooltipContent = document.createElement('div');
      tooltipContent.innerHTML = `
    <div>
      <table style="border-collapse: collapse; width: 200px; background-color: #f4f4f4;">
      <tr>
          <td style="border: 1px solid #d3d3d3; padding: 5px; background-color: #e9e9e9;">Name:</td>
          <td style="border: 1px solid #d3d3d3; padding: 5px;">${obj.Name}</td>
      </tr>
        <tr>
          <td style="border: 1px solid #d3d3d3; padding: 5px; background-color: #e9e9e9;">Role:</td>
          <td style="border: 1px solid #d3d3d3; padding: 5px;">${obj.Designation}</td>
        </tr>
        <tr>
          <td style="border: 1px solid #d3d3d3; padding: 5px; background-color: #e9e9e9;">Empoyee ID:</td>
          <td style="border: 1px solid #d3d3d3; padding: 5px;">${obj.EmployeeID}</td>
        </tr>
        <tr>
          <td style="border: 1px solid #d3d3d3; padding: 5px; background-color: #e9e9e9;">Team:</td>
          <td style="border: 1px solid #d3d3d3; padding: 5px;">${obj.Team}</td>
        </tr>
        <tr>
          <td style="border: 1px solid #d3d3d3; padding: 5px; background-color: #e9e9e9;">Email ID::</td>
          <td style="border: 1px solid #d3d3d3; padding: 5px;">${obj.EmailId}</td>
        </tr>
        <tr>
          <td style="border: 1px solid #d3d3d3; padding: 5px; background-color: #e9e9e9;">Phone Number:</td>
          <td style="border: 1px solid #d3d3d3; padding: 5px;">${obj.PhoneNumber}</td>
        </tr>
      </table>
    </div>`;
      return tooltipContent;
    }
    //Connector defaults to customize connector
    const connDefaults = (connector, diagram) => {
      connector.targetDecorator.shape = 'None';
      connector.type = 'Orthogonal';
      connector.constraints = ConnectorConstraints.Default & ~ConnectorConstraints.Select;
      connector.style.strokeColor = 'gray';
      return connector;
    };

    const setNodeTemplate = nodeTemplate.bind(this);

    //Node template to add image and text node
    function nodeTemplate(obj) {
      let content = new StackPanel();
      content.id = obj.id + '_outerstack';
      content.orientation = 'Horizontal';
      content.style.strokeColor = (obj).data.StrokeColor;
      content.style.fill = (obj).data.Fill;
      content.padding = { left: 5, right: 10, top: 5, bottom: 5 };

      //Add the line at the top of the outer stack
      let line = new PathElement();
      line.data = 'M0,0 L1,0'; // Line from (0,0) to (1,0)
      line.width = 2;
      line.height = 1;
      line.style.strokeWidth = 2;
      (line.style).margin = { left: 20, right: 20, top: 20, bottom: 20 };
      line.style.strokeColor = ((obj).data).RatingColor;
      line.horizontalAlignment = 'Stretch';
      line.verticalAlignment = 'Top';
      line.id = obj.id + '_line';

      let image = new ImageElement();
      image.width = 50;
      image.height = 50;
      image.source = ((obj).data).ImageUrl ? ((obj).data).ImageUrl : '';
      image.id = obj.id + '_pic';
      image.style.strokeColor = 'transparent';
      image.style.fill = 'transparent';

      let innerStack = new StackPanel();
      innerStack.style.strokeColor = 'transparent';
      innerStack.style.fill = 'transparent';
      innerStack.margin = { left: 5, right: 0, top: 0, bottom: 0 };
      innerStack.id = obj.id + '_innerstack';

      let text = new TextElement();
      text.content = ((obj).data).Name;
      text.style.color = (obj).data.color;
      text.style.bold = (obj).addInfo ? (obj).addInfo.IsBold : (obj).addInfo;
      text.style.italic = (obj).data.IsItalic;
      text.style.textDecoration = (obj).data.Decoration;
      text.style.fontSize = (obj).data.FontSize;
      text.style.fontFamily = (obj).data.FontFamily;
      text.style.strokeColor = 'none';
      text.horizontalAlignment = 'Left';
      text.style.fill = 'none';
      text.id = obj.id + '_text1';

      let desigText = new TextElement();
      desigText.margin = { left: 0, right: 0, top: 5, bottom: 0 };
      desigText.content = ((obj).data).Designation;
      desigText.style.color = (obj).data.color;
      desigText.style.bold = (obj).addInfo? (obj).addInfo.IsBold: (obj).addInfo;
      desigText.style.italic = (obj).data.IsItalic;
      desigText.style.textDecoration = (obj).data.Decoration;
      desigText.style.fontSize = (obj).data.FontSize;
      desigText.style.fontFamily = (obj).data.FontFamily;
      desigText.style.strokeColor = 'none';
      desigText.style.fill = 'none';
      desigText.horizontalAlignment = 'Left';
      desigText.style.textWrapping = 'Wrap';
      desigText.id = obj.id + '_desig';

      innerStack.children = [text, desigText];

      // Add the line to the innerStack, and the innerStack to the content stack
      innerStack.children = [ line, text, desigText];
      content.children = [image, innerStack];

      return content;
    };

    const selectionChange = (args) => {
      clientSideEvents.selectionChange(args);
    };
    //Selected Items to enable userhandles
    const selectedItems = {
      constraints: SelectorConstraints.All,
      userHandles: handles
    };
    //scrollSettings to assign minZoom and maxZoom
    const scrollSettings = {
      minZoom: 0.3, maxZoom: 3.2
    };

    const scrollChange = (args) => {
      clientSideEvents.scrollChange(args);
    };
    const historyChange = (args) => {
      clientSideEvents.historyChange(args);
    };
    //PageSettings to customize the diagram page
    const pageSettings = {
      background: { color: '#FFFFFF' }, margin: { left: 5, top: 5 },
      orientation: 'Landscape', showPageBreaks: false, multiplePage: false
    };
    const onUserHandleMouseDown = (args) => {
      clientSideEvents.onUserHandleMouseDown(args);
    }
    const horizontalSpacing = 30
    const verticalSpacing = 30
    const zoomLevel = "100 %"
    const header = 'Properties';
    const exportHeader = 'Export Diagram';
    const searchHeader = "Search Nodes";
    const animationSettings = { effect: 'None' };
    const showCloseIcon = true;

    //Buttons to export the diagram
    const exportingButtons = computed(() => {
      return UtilityMethods.prototype.getDialogButtons('export');
    });

    //Buttons to edit the nodes
    const defaultButtons = computed(() => {
      return UtilityMethods.prototype.getDialogButtons('defaultButtons');
    });

    const searchButtons = computed(() => {
      return UtilityMethods.prototype.getDialogButtons('search');
    });

    const multiCheckboxValue = [
      'Name', 'Desig'
    ];
    const fileName = "Untitled Diagram"
    const formatValue = "JPG"
    const regionsValue = "Content"
    const fontFamilyValue = "Arial"
    const fontSizeValue = "12"
    return {
      dropDownDataSources,
      visible,
      width,
      height,
      layout,
      data,
      rulerSettings,
      snapSettings,
      nodeDefaults,
      connDefaults,
      setNodeTemplate,
      selectionChange,
      selectedItems,
      scrollSettings,
      scrollChange,
      historyChange,
      pageSettings,
      onUserHandleMouseDown,
      horizontalSpacing,
      verticalSpacing,
      zoomLevel,
      header,
      searchHeader,
      animationSettings,
      showCloseIcon,
      defaultButtons,
      searchButtons,
      multiCheckboxValue,
      formatValue,
      regionsValue,
      fontFamilyValue,
      fontSizeValue,
      path,
      position,
      exportHeader,
      exportingButtons,
      fileName
    }

  },

  mounted: function () {
    diagramInstance = this.$refs.diagramRef.ej2Instances;
    defaultUploaderInstance = this.$refs.defaultUploadRef.ej2Instances;
    pictureUploadInstance = this.$refs.pictureUploadRef.ej2Instances;
    expandableInstance = this.$refs.expandableRef.ej2Instances;
    utilityMethods = new UtilityMethods();
    utilityMethods.showHidePictures = []
    clientSideEvents = new DiagramClientSideEvents();
  },

  computed: {

  },

  methods: {
    //Created event
    created(args) {
      diagramInstance = this.$refs.diagramRef.ej2Instances;
      diagramInstance.fitToPage({ mode: 'Page', region: 'Content' });
      diagramInstance.zoomTo({ type: 'ZoomOut', zoomFactor: 0.2 });
    },
    collectionChange(args) {
      if (args.state === 'Changed' && args.type === 'Removal') {
            let node = args.element;
            let data = node.data;
            if (data) {
                let filterData = diagramInstance.dataSourceSettings.dataSource.dataSource.json.filter(x=>x.Name === data.Name);
                let index = diagramInstance.dataSourceSettings.dataSource.dataSource.json.indexOf(filterData[0]);
                diagramInstance.dataSourceSettings.dataSource.dataSource.json.splice(index,1)
            }
        }
    },

    getImagePath(imageName) {
      return require(`@/assets/${imageName}`);
    },

    // Rename Implementation - Start
    renameDiagram(args) {
      document.getElementsByClassName('db-diagram-name-container')[0].classList.add('db-edit-name');
      let element = (document.getElementById('diagramEditable'));
      element.value = (document.getElementById('diagramName')).innerHTML;
      element.focus();
      element.select();
    },
    // Event handler triggered when the diagram name is changed.
    diagramNameKeyDown(args) {
      if (args.which === 13) {
        (document.getElementById('diagramName')).innerHTML = (document.getElementById('diagramEditable')).value;
        document.getElementsByClassName('db-diagram-name-container')[0].classList.remove('db-edit-name');
      }
    },
    diagramNameChange(args) {
      document.getElementById('diagramName').innerHTML = document.getElementById('diagramEditable').value;
      document.getElementsByClassName('db-diagram-name-container')[0].classList.remove('db-edit-name');
      document.getElementById("exportfileName").value = document.getElementById('diagramName').innerHTML;
    },
    //Method to do opertation of the menu bar
    menuSelect(args) {
      let options = args.item.text;
      let zoomSlider = (document.getElementById("zoomSlider")).ej2_instances[0];
      var exportDialog = (document.getElementById("exportDialog")).ej2_instances[0];
      switch (options) {
        case 'New':
          debugger;
          (diagramInstance).clear();
          clientSideEvents.historyChange();

          break;
        case 'Open':
          (document.getElementsByClassName('e-file-select-wrap')[0].querySelector('button')).click();
          break;
        case 'Save':
          let data = diagramInstance?.saveDiagram();
          utilityMethods.download(data);
          break;
        case 'Export':
          exportDialog.show();
          break;
        case 'Print':
          utilityMethods.btnPrintClick();
          break;
        case 'Landscape':
          (args.item).parentObj.items[1].iconCss = '';
          args.item.iconCss = 'sf-icon-check-tick';
          (diagramInstance).pageSettings.orientation = 'Landscape';
          break;
        case 'Portrait':
          (args.item).parentObj.items[0].iconCss = '';
          args.item.iconCss = 'sf-icon-check-tick';
          (diagramInstance).pageSettings.orientation = 'Portrait';
          break;
        case 'Letter (8.5 in x 11 in)':
        case 'Legal (8.5 in x 14 in)':
        case 'A3 (297 mm x 420 mm)':
        case 'A4 (210 mm x 297 mm)':
        case 'A5 (148 mm x 210 mm)':
        case 'A6 (105 mm x 148 mm)':
        case 'Tabloid (279 mm x 432 mm)':
          utilityMethods.paperListChange(args);
          utilityMethods.updateSelection(args.item)
          break;
        case 'Show Lines':
          (diagramInstance).snapSettings.constraints = diagramInstance.snapSettings.constraints ^ SnapConstraints.ShowLines;
          args.item.iconCss = args.item.iconCss ? '' : 'sf-icon-check-tick';
          break;
        case 'Snap To Grid':
          (diagramInstance).snapSettings.constraints = (diagramInstance).snapSettings.constraints ^ SnapConstraints.SnapToLines;
          args.item.iconCss = args.item.iconCss ? '' : 'sf-icon-check-tick';
          break;
        case 'Snap To Object':
          (diagramInstance).snapSettings.constraints = (diagramInstance).snapSettings.constraints ^ SnapConstraints.SnapToObject;
          args.item.iconCss = args.item.iconCss ? '' : 'sf-icon-check-tick';
          break;
        case 'Show Ruler':
          args.item.iconCss = args.item.iconCss ? '' : 'sf-icon-check-tick';
          (diagramInstance).rulerSettings.showRulers = !(diagramInstance).rulerSettings.showRulers;
          break;
        case 'Show Page Breaks':
          args.item.iconCss = args.item.iconCss ? '' : 'sf-icon-check-tick';
          (diagramInstance).pageSettings.showPageBreaks = !(diagramInstance).pageSettings.showPageBreaks;
          break;
        case 'Show Multiple page':
          args.item.iconCss = args.item.iconCss ? '' : 'sf-icon-check-tick';
          (diagramInstance).pageSettings.multiplePage = !(diagramInstance).pageSettings.multiplePage;
          break;
        case 'Fit To Width':
          (diagramInstance).fitToPage({ mode: 'Width' });
          zoomSlider.content = Math.round((diagramInstance).scrollSettings.currentZoom * 100);
          break;
        case 'Fit To Page':
          (diagramInstance).fitToPage({ mode: 'Page', region: 'Content' });
          zoomSlider.content = Math.round((diagramInstance).scrollSettings.currentZoom * 100);
          break;
      }
      diagramInstance?.dataBind();
    },
    //Click Event for orientation of the PropertyPanel.
    handleOrientationClick(args) {
      var target = args.target;
      var diagram = this.$refs.diagramRef.ej2Instances;
      if (target.className.includes('image-pattern-style2')) {
        // Remove the 'e-selected-orientation-style' class from all divs with the class 'image-pattern-style'
        var divs = document.getElementsByClassName('image-pattern-style2');
        for (var i = 0; i < divs.length; i++) {
          divs[i].classList.remove('e-selected-orientation-style');
        }
        // Add the 'e-selected-orientation-style' class to the selected div
        target.classList.add('e-selected-orientation-style');
        // Update the diagram orientation based on the selected div
        var orientation1 = target.id;
        diagram.layout.orientation = orientation1;
        diagram.dataBind();
        diagram.doLayout();
        diagram.fitToPage({ mode: 'Page', region: 'Content' });
      }
    },
    //Method to change the subtree option
    subTreeClick(args) {
      var target = args.target;
      var diagram = this.$refs.diagramRef.ej2Instances;
      if (target.className === 'image-pattern-style') {
        var subTreeOrientation;
        var subTreeAlignment;
        switch (target.id) {
          case 'pattern1':
            subTreeOrientation = 'Vertical';
            subTreeAlignment = 'Alternate';
            break;
          case 'pattern2':
            subTreeOrientation = 'Vertical';
            subTreeAlignment = 'Left';
            break;
          case 'pattern3':
            subTreeOrientation = 'Vertical';
            subTreeAlignment = 'Left';
            break;
          case 'pattern4':
            subTreeOrientation = 'Vertical';
            subTreeAlignment = 'Right';
            break;
          case 'pattern5':
            subTreeOrientation = 'Vertical';
            subTreeAlignment = 'Right';
            break;
          case 'pattern6':
            subTreeOrientation = 'Horizontal';
            subTreeAlignment = 'Balanced';
            break;
          case 'pattern7':
            subTreeOrientation = 'Horizontal';
            subTreeAlignment = 'Center';
            break;
          case 'pattern8':
            subTreeOrientation = 'Horizontal';
            subTreeAlignment = 'Left';
            break;
          case 'pattern9':
            subTreeOrientation = 'Horizontal';
            subTreeAlignment = 'Right';
            break;
          default:
            break;
        }
        // Remove the 'e-selected-orientation-style' class from all divs with the class 'image-pattern-style'
        var divs = document.getElementsByClassName('image-pattern-style');
        for (var i = 0; i < divs.length; i++) {
          divs[i].classList.remove('e-selected-pattern-style');
        }
        // Add the 'e-selected-orientation-style' class to the selected div
        target.classList.add('e-selected-pattern-style');
        diagram.layout.getLayoutInfo = function (node, options) {
          if (target.id === 'pattern4' || target.id === 'pattern3') {
            options.offset = -50;
          }
          if (node.data.Role === 'General Manager') {
            options.assistants.push(options.children[0]);
            options.children.splice(0, 1);
          }
          if (!options.hasSubTree) {
            options.orientation = subTreeOrientation;
            options.type = subTreeAlignment;
          }
        };
        diagram.dataBind();
        diagram.doLayout();
        diagram.fitToPage({ mode: 'Page', region: 'Content' });
      }
    },
    //Numeric text box to change the horizontal spacing
    onhSpacingChange(args) {
      var diagramInstance = this.$refs.diagramRef.ej2Instances;
      diagramInstance.layout.horizontalSpacing = args.value
      diagramInstance.dataBind();
    },
    //Numeric text box to change the vertical spacing
    onvSpacingChange(args) {
      var diagramInstance = this.$refs.diagramRef.ej2Instances;
      diagramInstance.layout.verticalSpacing = args.value
      diagramInstance.dataBind();
    },
    // Update zoom level
    updateZoomLevel(args) {
      utilityMethods.zoomChange(args)
    },
    // Toolbar click
    toolbarClick(args) {
      utilityMethods.toolbarclick(args);
    },
    // Insert or remove picture
    insertOrRemovePicture(args) {
      utilityMethods.insertOrRemovePicture(args);
    },
    // Modify node template
    modifyNodeTemplate(args) {
      utilityMethods.modifyNodeTemplate(args);
    },
    //Check box to expand and collapse nodes
    onExpandChange(args) {
      var diagramInstance = this.$refs.diagramRef.ej2Instances;
      for (let node of diagramInstance.nodes) {
        if (args.checked) {
          node.expandIcon.shape = 'Minus';
          node.collapseIcon.shape = 'Plus';
        }
        else {
          node.expandIcon.shape = 'None';
          node.collapseIcon.shape = 'None';
        }
      }
      diagramInstance.dataBind();
      diagramInstance.doLayout();
    },
    searchBtn(){
      var searchDialog = document.getElementById("searchDialog").ej2_instances[0];
      searchDialog.show();
    },
    // To close the overview when we click the close button.
    toggleOverviewButton() {
      let toolbarObj = (document.getElementById("toolbarEditor")).ej2_instances[0];
      (document.getElementById('overview-container')).style.position = '';
      (document.getElementById('overview-container')).style.zIndex = '-1';
      toolbarObj.items[toolbarObj.items.length - 1].cssClass = toolbarObj.items[toolbarObj.items.length - 1].cssClass.replace('active', '');
    },
    // Re-layout the diagram
    reLayout() {
      var diagram = this.$refs.diagramRef.ej2Instances;
      diagram.doLayout();
    },
    // Add an assistant node
    addAssistant() {
      DiagramClientSideEvents.prototype.addChild();
    },
    
    // Triggers when the JSON file is uploaded successfully. 
    onUploadSuccess(args) {
      let file1 = args.file;
      let file = (file1).rawFile;
      var reader = new FileReader();
      reader.readAsText(file);
      reader.onloadend = this.loadDiagram.bind(this);
      defaultUploaderInstance.clearAll();
    },
    onPictureUploadSuccess(args) {
      const file = args.file;
      const reader = new FileReader();
      reader.onload = function () {
        const base64String = reader.result;
        var diagram = document.getElementById("diagram").ej2_instances[0];
        let selectedNode = diagram.selectedItems.nodes[0];
        selectedNode.data.ImageUrl = base64String;
        diagram.dataSourceSettings.dataSource.dataSource.json.find(x => x.Id == selectedNode.data.Id).ImageUrl = base64String;
        var imageTag = document.getElementById(selectedNode.id + '_picimage');
        imageTag.href.baseVal = base64String;
        UtilityMethods.prototype.addImageToWrapper(selectedNode,undefined,base64String);
        pictureUploadInstance.clearAll();
      };
      reader.readAsDataURL(file.rawFile);
    },

    loadDiagram(event) {
      var diagram = this.$refs.diagramRef.ej2Instances;
      diagram.loadDiagram((event.target).result);
      diagram.fitToPage({ mode: 'Page', region: 'Content' });
    },
    //Method to handle whether the upload will be failed
    onUploadFailure(args) {
      (document.getElementsByClassName('sb-content-overlay')[0]).style.display = 'none';
    },
    // Handle file selection during upload
    onUploadFileSelected(args) {
      (document.getElementsByClassName('sb-content-overlay')[0]).style.display = 'none';
    },
    
    // Handle progress during file upload
    onUploadProgress(args) {
      (document.getElementsByClassName('sb-content-overlay')[0]).style.display = 'none';
    },
    // Open menu item
    onOpen(args) {
      var multiCheckbox = (document.getElementById("multiCheckbox")).ej2_instances[0];
      const lis = multiCheckbox.liCollections[0];
      lis.classList.add('e-disabled');
      lis.style.pointerEvents = 'auto';
    },
    // Modify node template through checkbox
    checkboxmodifyNodeTemplate(args) {
      utilityMethods.modifyNodeTemplate(args);
    },
    //Method to change the fontfamily of the selected node
    fontFamilyChange(args) {
      if ((args).value) {
        (args).value = null;
      }
      utilityMethods.fontStyleChange(args);
    },
    //Method to change the size of the font of the selected node
    fontSizeChange(args) {
      utilityMethods.fontStyleChange(args);
    },
    //Method to change the font color of the selected node
    fontColorChange(args) {
      let parameter = { itemData: { text: args.value }, itemValue: 'fontColor' };
      utilityMethods.fontStyleChange(parameter);
    },
    //Method to change the stroke color of the selected node
    strokeColorChange(args) {
      let diagram = this.$refs.diagramRef.ej2Instances;
      let strokeColor = args.value;
      for (let i = 0; i < diagram.selectedItems.nodes.length; i++) {
        let outerContainer = document.getElementById(diagram.selectedItems.nodes[i].id + '_outerstack');
        (outerContainer).style.stroke = strokeColor;
        diagram.selectedItems.nodes[i].data.StrokeColor = strokeColor;
      }
      diagram.dataBind();
    },
    //Method to change the fill color of the selected node
    fillColorChange(args) {
      let diagram = this.$refs.diagramRef.ej2Instances;
      let fillColor = args.value;
      for (let i = 0; i < diagram.selectedItems.nodes.length; i++) {
        let outerContainer = document.getElementById(diagram.selectedItems.nodes[i].id + '_outerstack');
        (outerContainer).style.fill = fillColor;
        diagram.selectedItems.nodes[i].addInfo.fill = fillColor;
        diagram.selectedItems.nodes[i].data.Fill = fillColor;
      }
      diagram.dataBind();
    }

  },
  provide: {
    diagram: [DataBinding, HierarchicalTree, PrintAndExport, Snapping, UndoRedo],
    multiselect: [CheckBoxSelection]
  }

})


</script>
